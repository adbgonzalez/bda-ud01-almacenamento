# UD1 – Bloque 5: Delta Lake sobre HDFS e S3 (MinIO)

## Obxectivos da práctica

- Comprender o concepto de **Delta Lake** e o modelo *Lakehouse*.
- Engadir funcionalidades **ACID** e de **versionado** a un Data Lake existente.
- Crear e consultar táboas Delta sobre **HDFS** e **MinIO/S3**.
- Aplicar operacións de escritura, actualización e *time travel*.
- Avaliar as vantaxes fronte a formatos tradicionais (Parquet, CSV).

---

## 1. Teoría: de Data Lake a Lakehouse

### O problema

Os Data Lakes tradicionais (HDFS, S3) permiten almacenar grandes volumes de datos,
pero non garantizan:
- Consistencia entre lecturas e escrituras.
- Control de versións.
- Evolución do esquema.
- Integridade ante procesos concorrentes.

Isto dificulta o mantemento e a fiabilidade dos fluxos de datos analíticos.

### A solución: Delta Lake

**Delta Lake** é unha capa de almacenamento *open source* que se coloca enriba de HDFS ou S3 e engade:

| Funcionalidade | Descrición |
|----------------|-------------|
| **ACID transactions** | Escritas e actualizacións atómicas e consistentes. |
| **Schema enforcement** | Impide introducir datos incompatibles co esquema. |
| **Time travel** | Permite consultar versións anteriores dos datos. |
| **Upserts e merges** | Actualización eficiente de rexistros existentes. |

### Arquitectura simplificada

```
┌─────────────────────────────┐
│          Spark SQL          │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│        Delta Engine         │  ← Control de transaccións, logs e versións
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│       HDFS / S3 (MinIO)     │  ← Almacenamento físico (Parquet + _delta_log)
└─────────────────────────────┘
```

---

## 2. Preparación do contorno

### Requisitos

- Clúster Spark operativo (por exemplo, [spark-cluster](https://github.com/adbgonzalez/spark-cluster))
- Hadoop/HDFS ou MinIO accesible.
- Python ≥ 3.8
- Paquete Delta Lake instalado.

### Iniciar unha sesión PySpark con Delta Lake

```bash
pyspark   --packages io.delta:delta-spark_2.12:3.1.0   --conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension"   --conf "spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog"
```

Se traballas dende Jupyter ou VS Code:
```python
from delta import configure_spark_with_delta_pip
from pyspark.sql import SparkSession

builder = (
    SparkSession.builder
    .appName("DeltaLakeDemo")
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
)

spark = configure_spark_with_delta_pip(builder).getOrCreate()
```

---

## 3. Escenario 1 — Delta Lake sobre HDFS

### 3.1. Crear un DataFrame de exemplo

```python
data = [
    ("2024-01-01", "A Coruña", 11.2),
    ("2024-01-01", "Vigo", 13.5),
    ("2024-01-02", "A Coruña", 9.8),
    ("2024-01-02", "Vigo", 14.1)
]

df = spark.createDataFrame(data, ["data", "cidade", "temperatura"])
df.show()
```

### 3.2. Gardar como Delta Table

```python
df.write.format("delta").mode("overwrite").save("hdfs:///datalake/meteo_delta")
```

Comproba en HDFS:
```bash
hdfs dfs -ls /datalake/meteo_delta
hdfs dfs -ls /datalake/meteo_delta/_delta_log
```

### 3.3. Lectura e consulta

```python
df_delta = spark.read.format("delta").load("hdfs:///datalake/meteo_delta")
df_delta.createOrReplaceTempView("meteo")

spark.sql("SELECT cidade, AVG(temperatura) as media FROM meteo GROUP BY cidade").show()
```

### 3.4. Actualización (upsert)

```python
from delta.tables import DeltaTable

delta_table = DeltaTable.forPath(spark, "hdfs:///datalake/meteo_delta")

novos_datos = [
    ("2024-01-02", "A Coruña", 10.2),
    ("2024-01-03", "Ourense", 6.4)
]
df_novos = spark.createDataFrame(novos_datos, ["data", "cidade", "temperatura"])

delta_table.alias("old").merge(
    df_novos.alias("new"),
    "old.data = new.data AND old.cidade = new.cidade"
).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
```

### 3.5. Versionado e *time travel*

```python
# Consulta da versión actual
spark.sql("DESCRIBE HISTORY delta.`hdfs:///datalake/meteo_delta`").show(truncate=False)

# Ler versión anterior (versionAsOf)
df_v0 = spark.read.format("delta").option("versionAsOf", 0).load("hdfs:///datalake/meteo_delta")
df_v0.show()
```

---

## ☁️ 4. Escenario 2 — Delta Lake sobre MinIO (S3A)

### 4.1. Configurar conexión a MinIO

Editar o `core-site.xml` de Hadoop:

```xml
<property>
  <name>fs.s3a.access.key</name>
  <value>admin</value>
</property>
<property>
  <name>fs.s3a.secret.key</name>
  <value>supersecret</value>
</property>
<property>
  <name>fs.s3a.endpoint</name>
  <value>http://minio:9000</value>
</property>
<property>
  <name>fs.s3a.path.style.access</name>
  <value>true</value>
</property>
```

Verifica dende Hadoop:
```bash
hadoop fs -mkdir s3a://deltalake/
```

### 4.2. Gardar e ler en MinIO

```python
df.write.format("delta").mode("overwrite").save("s3a://deltalake/meteo_delta")

df_s3 = spark.read.format("delta").load("s3a://deltalake/meteo_delta")
df_s3.show()
```

### 4.3. Comprobar en MinIO UI
Accede á consola (http://localhost:9001) e observa:
```
Bucket: deltalake/
 ├── meteo_delta/
 ├── _delta_log/
 └── part-*.parquet
```

---

## 🧪 5. Actividades propostas

| Actividade | Descrición |
|-------------|------------|
| **A1. Delta básica** | Crear unha táboa Delta sobre HDFS a partir dun CSV. Consultar o `_delta_log` e contar versións. |
| **A2. Time Travel** | Realizar 3 actualizacións e recuperar os datos da versión inicial. |
| **A3. Delta en MinIO** | Repetir o proceso pero gardando en `s3a://deltalake/demo`. Comprobar que Spark le correctamente. |
| **A4. Comparativa Parquet vs Delta** | Medir o tempo de lectura de ambas versións e analizar diferenzas. |
| **A5. Capas Medallion** | Crear tres directorios: `/bronze`, `/silver`, `/gold` e copiar/limpar datos progresivamente con Delta. |

---

## 🧭 6. Conclusións

- Delta Lake converte HDFS ou S3 nun **almacenamento transaccional e versionado**.
- Permite *time travel* e operacións ACID, achegando o mundo analítico ao de bases de datos.
- Serve de base para arquitecturas **Medallion** e **Lakehouse**.
- No ecosistema educativo, permite traballar con procesos reais sen depender de solucións comerciais.

---

## 📚 7. Referencias

- [Delta Lake Documentation](https://docs.delta.io/latest/)
- [Apache Spark 3.x Documentation](https://spark.apache.org/docs/latest/)
- [MinIO Docs](https://min.io/docs/minio)
- [Repositorio Spark Cluster de adbgonzalez](https://github.com/adbgonzalez/spark-cluster)

# 🗂️ Uso de MinIO como almacenamento S3 no teu clúster Spark

## 1. Que é MinIO?

**MinIO** é unha solución de almacenamento de obxectos **compatible co protocolo S3** de Amazon Web Services. Está deseñada para ser **lixeira, rápida e autoaloxada**, ideal para laboratorios e contornos Big Data locais.

En lugar de gardar ficheiros nun sistema de ficheiros clásico (HDFS, ext4, etc.), MinIO organiza os datos en:
- **Buckets** (equivalentes a “directorios raíz”)
- **Obxectos** (ficheiros binarios ou texto)
- **Metadatos** (información sobre permisos, tamaño, tipo, etc.)

---

## 2. Arquitectura básica

Nun clúster como o teu, MinIO funciona como **servizo adicional** dentro da rede Docker (`network_cluster`), ofrecendo unha API S3 accesible dende Spark, Hadoop ou outras aplicacións.

```
[Spark Master] --\
[Spark Workers] --- > [MinIO Server] <--> [minio/mc client]
[Jupyter Notebook] /
```

- Porto **9000** → API S3 (para Spark, Hadoop, `mc`, etc.)
- Porto **9001** → consola web (para xestionar buckets e obxectos)

---

## 3. Engadir MinIO ao `docker-compose`

Engade ao teu ficheiro `docker-compose.yml` o seguinte bloque (axustando portos se xa usas o 9000):

```yaml
services:
  minio:
    image: minio/minio:latest
    container_name: minio
    command: server /data --console-address ":9001"
    environment:
      - MINIO_ROOT_USER=minio
      - MINIO_ROOT_PASSWORD=minio12345
    ports:
      - "9000:9000"  # API S3
      - "9001:9001"  # Consola web
    volumes:
      - ./minio_data:/data
    networks:
      - network_cluster
```

> **Acceso web:** [http://localhost:9001](http://localhost:9001)  
> **Usuario:** `minio`  
> **Contrasinal:** `minio12345`

---

## 4. Configuración en Spark (S3A Connector)

Para que Spark poida ler e escribir en MinIO, hai que engadir o **conector S3A** e a súa configuración.

### 4.1. Engadir as JARs necesarias

Copia estes dous ficheiros ao cartafol `./jars` e móntao en todos os servizos Spark:

- `hadoop-aws-3.3.6.jar`
- `aws-java-sdk-bundle-1.12.x.jar`

```yaml
volumes:
  - ./jars:/opt/spark/jars
```

### 4.2. Engadir configuración en `spark-defaults.conf` ou variables de contorno

#### 🧰 Opción 1: variables de contorno (sinxela para Jupyter)
```yaml
environment:
  - SPARK_MASTER_URL=spark://spark-master:7077
  - SPARK_EVENTLOG_ENABLED=true
  - SPARK_EVENTLOG_DIR=hdfs:///spark/logs/history
  - SPARK_EXTRA_CONF=\
      spark.hadoop.fs.s3a.access.key=minio,\
      spark.hadoop.fs.s3a.secret.key=minio12345,\
      spark.hadoop.fs.s3a.endpoint=http://minio:9000,\
      spark.hadoop.fs.s3a.path.style.access=true,\
      spark.hadoop.fs.s3a.connection.ssl.enabled=false,\
      spark.hadoop.fs.s3a.impl=org.apache.hadoop.fs.s3a.S3AFileSystem
```

#### 🧩 Opción 2: editar `/opt/spark/conf/spark-defaults.conf`

```
spark.hadoop.fs.s3a.access.key          minio
spark.hadoop.fs.s3a.secret.key          minio12345
spark.hadoop.fs.s3a.endpoint            http://minio:9000
spark.hadoop.fs.s3a.path.style.access   true
spark.hadoop.fs.s3a.connection.ssl.enabled false
spark.hadoop.fs.s3a.impl                org.apache.hadoop.fs.s3a.S3AFileSystem
```

---
## 5. Creación e xestión de buckets en MinIO

### 📘 Introdución teórica

En MinIO, igual que en Amazon S3, o **bucket** é a unidade lóxica principal de almacenamento.  
Cada bucket actúa como un *contenedor* onde se gardan os obxectos (ficheiros ou blobs), e pode configurarse con políticas de acceso, versións, cifrado e outros parámetros.

Pódese pensar nun bucket como o equivalente a un **“directorio raíz”**, pero cunha diferenza clave:  
mentres que un sistema de ficheiros tradicional almacena datos en bloques nunha árbore de directorios, un sistema de obxectos como MinIO usa unha estrutura **plana**, onde cada obxecto está identificado por unha **chave única** (key).

---

### 🧩 Estrutura conceptual dun bucket

Un bucket almacena obxectos cun formato de ruta tipo:

```
s3a://nome_do_bucket/ruta/ata/o/ficheiro.csv
```

Cada obxecto ten:
- **Key:** a ruta completa (“datasets/2025/temperaturas.csv”)
- **Valor:** o contido binario
- **Metadatos:** información adicional (data de creación, tamaño, tipo MIME, versión...)

💡 En MinIO, a clave (“key”) pódese usar para simular subdirectorios, pero realmente non existen carpetas:  
é só un prefixo lexicográfico.

---

### 🧠 Exemplo visual

Se creas tres obxectos cos nomes seguintes:

```
s3a://datasets/2025/enero.csv
s3a://datasets/2025/febrero.csv
s3a://datasets/2024/enero.csv
```

No navegador web de MinIO parecerán directorios (`2024`, `2025`), pero internamente son só *keys*:

| Key | Tamaño | Última modificación |
|-----|---------|---------------------|
| 2024/enero.csv | 12 KB | 2025-01-01 |
| 2025/enero.csv | 14 KB | 2025-02-01 |
| 2025/febrero.csv | 13 KB | 2025-03-01 |

---

### ⚙️ Ferramentas para xestionar buckets

Podes xestionar MinIO de tres formas principais:

1. **Consola web** (en `http://localhost:9001`)  
   Ideal para tarefas sinxelas: crear buckets, subir ficheiros, ver obxectos, establecer políticas públicas/privadas.

2. **CLI oficial `mc` (MinIO Client)**  
   Ferramenta de liña de comandos potente e automatizable.  
   A súa sintaxe lembra a de `aws cli` de Amazon.

3. **API S3 / SDKs**  
   Usada por aplicacións e frameworks (por exemplo, Spark, Hadoop, Airflow, etc.).

---

### 🧰 Uso práctico do cliente `mc`

#### 1️⃣ Crear un contedor interactivo de `mc`

Se non tes o cliente instalado localmente, podes usalo directamente dende un contedor Docker:

```bash
docker run --network network_cluster --rm -it minio/mc sh
```

Explicación:
- `--network network_cluster`: conecta co mesmo *bridge network* que MinIO e Spark.
- `--rm`: elimina o contedor ao saír.
- `-it`: modo interactivo.
- `minio/mc`: imaxe oficial do cliente MinIO.

---

#### 2️⃣ Definir un alias (punto de conexión)

Antes de facer calquera operación, tes que rexistrar o servidor ao que te vas conectar.

```bash
mc alias set local http://minio:9000 minioadmin minioadmin
```

Significado dos parámetros:

| Parámetro | Descrición |
|------------|-------------|
| `local` | Nome do alias (pode ser calquera identificador) |
| `http://minio:9000` | Endpoint do servidor MinIO (usa o nome do contedor) |
| `minioadmin` | Usuario raíz (MINIO_ROOT_USER) |
| `minioadmin` | Contrasinal (MINIO_ROOT_PASSWORD) |

Unha vez creado o alias, pódese usar “`local`” en lugar de escribir o endpoint completo.

💡 Podes ver os alias rexistrados con:
```bash
mc alias list
```

---

#### 3️⃣ Crear un novo bucket

```bash
mc mb local/spark
```

Explicación:
- `mb` → *make bucket*  
- `local` → alias definido antes  
- `spark` → nome do bucket

O nome do bucket debe cumprir certas regras:
- Só letras minúsculas, números e guións (`-`)
- Sen maiúsculas, nin espazos, nin guións baixos
- Debe ser único dentro da instalación

Exemplo de nomes válidos:
```
datasets, spark-logs, bronze, silver, gold
```

---

#### 4️⃣ Listar buckets e obxectos

```bash
mc ls local
```
Mostra a lista de buckets dispoñibles.

```bash
mc ls local/spark
```
Mostra o contido do bucket `spark` (obxectos e prefixos).

Podes engadir `--recursive` para ver toda a estrutura.

---

#### 5️⃣ Subir e descargar ficheiros

Subir un ficheiro:
```bash
mc cp datos.csv local/spark/
```

Descargar:
```bash
mc cp local/spark/datos.csv .
```

Subir directorios enteiros:
```bash
mc cp --recursive ./datasets local/spark/
```

💡 O cliente `mc` calcula *hashes* (ETags) e reintenta automaticamente se hai cortes de conexión.

---

#### 6️⃣ Eliminar buckets ou obxectos

Eliminar un obxecto:
```bash
mc rm local/spark/datos.csv
```

Eliminar un bucket completo:
```bash
mc rb --force local/spark
```

A opción `--force` borra todos os obxectos antes de eliminar o bucket.

---

### 🧱 Configuración avanzada de buckets

MinIO permite engadir políticas de acceso a cada bucket:

```bash
mc anonymous set public local/datasets
```
Fai que o bucket `datasets` sexa accesible publicamente (por exemplo, para compartir con alumnos).

Outras opcións:
```bash
mc anonymous set none local/datasets   # privado
mc anonymous set download local/docs   # só lectura
```

Tamén podes ver e editar políticas JSON:
```bash
mc admin policy info local readwrite
mc admin policy list local
```

---

### 💡 Exemplo completo paso a paso

```bash
# 1. Crear un alias
mc alias set local http://minio:9000 minio minio12345

# 2. Crear un bucket "datasets"
mc mb local/datasets

# 3. Subir ficheiros CSV e Parquet
mc cp ./data/temperaturas.csv local/datasets/
mc cp ./data/spark_results.parquet local/datasets/

# 4. Listar contido
mc ls local/datasets

# 5. Cambiar permisos para facelo público
mc anonymous set download local/datasets

# 6. Comprobar acceso dende navegador
# Navega a http://localhost:9000/datasets/temperaturas.csv
```

---

### ⚖️ Comparativa entre métodos de xestión

| Método | Vantaxes | Limitacións |
|--------|-----------|-------------|
| **Consola web** | Intuitiva, visual, ideal para demostracións | Limitada para automatización |
| **Cliente `mc`** | Potente, guións automáticos, control de permisos | Require coñecer comandos |
| **API / SDKs (Python, Java, etc.)** | Integración directa con aplicacións e frameworks | Máis complexidade de configuración |

---

### 🧠 Conclusión

O cliente `mc` é a forma máis flexible e profesional de xestionar MinIO.  
Permite crear, listar, eliminar e configurar buckets de maneira reproducible, ideal para contornos docentes, laboratorios e integración con ferramentas de Big Data como **Apache Spark**.

Unha vez configurados os teus buckets, Spark pode acceder a eles directamente empregando rutas `s3a://`:

```python
df = spark.read.csv("s3a://datasets/temperaturas.csv", header=True)
df.show()
```

Deste xeito, MinIO convértese nun **almacenamento unificado** para experimentos, datos de produción ou proxectos multiusuario, funcionando coma un S3 privado local.

---

## 6. Uso básico con Spark

### 6.1. Escribir datos a MinIO
```python
df = spark.range(0, 10)
df.write.mode("overwrite").parquet("s3a://spark/demo_parquet")
```

### 6.2. Ler datos desde MinIO
```python
df2 = spark.read.parquet("s3a://spark/demo_parquet")
df2.show()
```

### 6.3. Comprobación de conexión
```python
spark._jsc.hadoopConfiguration().get("fs.s3a.endpoint")
```
Debe devolver `http://minio:9000`.

---

## 7. Exercicios propostos

### 🧩 Exercicio 1: Verificar a conexión

1. Engade MinIO ao clúster e inícialo.  
2. Dende o Jupyter Notebook, executa:
   ```python
   spark._jsc.hadoopConfiguration().get("fs.s3a.endpoint")
   ```
3. Comproba que devolve o endpoint correcto.  
4. Proba un:
   ```python
   spark.range(5).write.csv("s3a://spark/test_csv")
   ```
   e verifica na consola web que se crearon ficheiros.

---

### 🧩 Exercicio 2: Lectura e escritura de distintos formatos

1. Crea un bucket `datasets` e sube un CSV manualmente dende a consola web.  
2. Léeo con Spark:
   ```python
   df = spark.read.option("header", True).csv("s3a://datasets/iris.csv")
   df.show()
   ```
3. Escribe o mesmo DataFrame en formato Parquet:
   ```python
   df.write.mode("overwrite").parquet("s3a://datasets/iris_parquet")
   ```

---

### 🧩 Exercicio 3: Uso combinado con HDFS

1. Escribe datos en HDFS:
   ```python
   df.write.mode("overwrite").parquet("hdfs:///tmp/hdfs_demo")
   ```
2. Copia o mesmo con:
   ```python
   df.write.mode("overwrite").parquet("s3a://spark/hdfs_copy")
   ```
3. Compara o tamaño de ambos directorios e comenta as diferenzas.

---

### 🧩 Exercicio 4: Configurar un *Data Lake* tipo Medallion

1. Crea tres buckets:
   - `bronze`
   - `silver`
   - `gold`
2. Define un fluxo:
   - Ler datos de `bronze` (CSV)
   - Limpalos e gardar en `silver` (Parquet)
   - Agregar e gardar en `gold` (Parquet consolidado)
3. Analiza tempos e tamaños resultantes.

---

## 8. Consellos e boas prácticas

- Emprega `path.style.access=true` sempre que uses MinIO local.  
- Desactiva SSL (`connection.ssl.enabled=false`) para contornos de laboratorio.  
- Crea buckets distintos para cada fase ou proxecto.  
- Adoita montar `minio_data` nun volume persistente.  
- Se usas MinIO nun contorno multiusuario, cambia as credenciais por variables seguras.

---

## 9. Recursos útiles

- [Documentación oficial de MinIO](https://min.io/docs/minio)  
- [Guía Hadoop S3A](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html)  
- [AWS SDK for Java](https://docs.aws.amazon.com/sdk-for-java/)  
- [Apache Spark – Cloud Integration](https://spark.apache.org/docs/latest/cloud-integration.html)

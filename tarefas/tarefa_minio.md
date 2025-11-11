## Tarefa MinIO
### Exercicio 1: Verificar a conexión

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

### Exercicio 2: Lectura e escritura de distintos formatos

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

### Exercicio 3: Uso combinado con HDFS

1. Escribe datos en HDFS:
   ```python
   df.write.mode("overwrite").parquet("hdfs:///tmp/hdfs_demo")
   ```
2. Copia o mesmo con:
   ```python
   df.write.mode("overwrite").parquet("s3a://spark/hdfs_copy")
   ```
3. Compara o tamaño de ambos directorios e observa as diferenzas.

---

### Exercicio 4: Configurar un *Data Lake* tipo Medallion

1. Crea tres buckets:
   - `bronze`
   - `silver`
   - `gold`
2. Define un fluxo:
   - Movemos `iris.csv`a `bronze`.
   - Ler datos de `bronze`, limpalos e escribir en silver (Parquet)
   ```python
      # partimos de df_bronze
      df_silver = (
         df_bronze
            .withColumnRenamed("sepal.length", "sepal_length")
            .withColumnRenamed("sepal.width",  "sepal_width")
            .withColumnRenamed("petal.length", "petal_length")
            .withColumnRenamed("petal.width",  "petal_width")  # petal_with → petal_width
      )  
   ```
   - Agregar e gardar en `gold` (Parquet consolidado)
   ```python
      from pyspark.sql import functions as F

      df_gold = (
         df_silver
            .groupBy("variety")
            .agg(
               F.avg("petal_length").alias("avg_petal_length"),
               F.avg("petal_width").alias("avg_petal_width")
            )
      )
   ```
3. Analiza tempos e tamaños resultantes.
   
> Entregar notebook cos exercicios nomeado seguindo o formato `apelido1_apelido2_nome.ipynb`
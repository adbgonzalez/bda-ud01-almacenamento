---
layout: default
title: Almacenamento
---

# Almacenamento en Big Data

---

## 1. Introdución ao almacenamento en Big Data

O almacenamento é un piar fundamental nas arquitecturas de Big Data. As características principais dos sistemas de almacenamento para grandes volumes de datos son:

- Escalabilidade horizontal (engadir máis nodos)
- Alta dispoñibilidade e tolerancia a fallos
- Capacidade para almacenar datos estruturados, semi-estruturados e non estruturados
- Soporte para diferentes formatos de datos (CSV, JSON, Parquet, Avro...)

---

## 2. Tipos de datos

### Datos estruturados:
- Datos organizados en filas e columnas (típicos dunha base de datos relacional)
- Fácilmente consultables con SQL
- Ex: rexistros de clientes, vendas, táboas relacionais

### Datos semi-estruturados:
- Teñen unha estrutura interna pero non fixada por esquemas ríxidos
- Ex: JSON, XML, logs de aplicacións

### Datos non estruturados:
- Non seguen ningunha estrutura predefinida
- Ex: imaxes, vídeos, documentos PDF, audio

![Tipos de datos Big Data](images/tipos-datos-bd-es.png)

Os sistemas Big Data deben ser capaces de xestionar todos estes tipos de datos de forma eficiente.

---

## 3. HDFS (Hadoop Distributed File System)

### Características:
- Sistema de ficheiros distribuído deseñado para traballar con grandes volumes de datos.
- Divide os ficheiros en bloques (por defecto 128MB) e distribóeos entre os nodos do clúster.
- Cada bloque ten varias réplicas (normalmente 3) para garantir tolerancia a fallos.

![Arquitectura HDFS](images/hdfs-arquitectura.png)

### Compoñentes principais:
- **NameNode**: xestiona a estrutura do sistema de ficheiros (metadatos).
- **DataNodes**: almacenan os bloques reais de datos.

### Vantaxes:
- Altamente escalable
- Tolerante a fallos
- Integración nativa con Hadoop e Spark

---

## 4. Almacenamento de obxectos: Amazon S3 e MinIO

### Amazon S3 (Simple Storage Service)
Amazon S3 é un servizo de almacenamento de obxectos na nube desenvolvido por AWS. Está deseñado para ofrecer almacenamento altamente escalable, duradeiro e accesible a través de Internet.

#### Principios de funcionamento:
- Os datos almacénanse como **obxectos**, non como ficheiros tradicionais.
- Cada obxecto inclúe os datos, metadatos e unha clave única.
- Os obxectos organízanse en **buckets**, que son contedores lóxicos.
- Os buckets pódense consultar e modificar a través dunha **API REST** ou mediante SDKs.
- A arquitectura garante alta durabilidade (11 noves: 99.999999999%) e redundancia xeográfica.

### MinIO como alternativa local compatible con S3
MinIO é unha solución lixeira de almacenamento de obxectos compatible coa API de S3. É ideal para:
- Entornos de desenvolvemento
- Probas locais
- Formación en Data Lakes sen depender da nube pública

![Amazon S3 / Minio](images/s3-minio.png)
#### Características:
- 100% compatible coa API de Amazon S3
- Instalación sinxela en local ou mediante contedores Docker
- Interface web amigable para xestionar buckets e obxectos
- Integración directa con ferramentas como Spark, Hive, Airflow mediante `s3a://`

#### Casos de uso:
- Creación dun **Data Lake local** baseado en obxectos
- Simulación de cargas en sistemas de almacenamento escalables
- Probas de lectura e escritura dende aplicacións distribuídas

---

## 5. Data Lakes, Data Warehouses e Lakehouses

### Data Lake:
- Almacena grandes volumes de datos en bruto en diferentes formatos
- Flexibilidade para datos estruturados e non estruturados
- Arquitectura baseada en almacenamento barato e escalable (ex: HDFS, S3, MinIO)

### Data Warehouse:
- Almacena datos limpos e estruturados preparados para analítica e BI
- Altamente optimizado para consultas SQL
- Ex: Google BigQuery, Snowflake, Redshift

### Lakehouse:
- Combina as vantaxes dos Data Lakes e Data Warehouses
- Permite traballar con datos brutos e estruturados nun só sistema
- Tecnoloxías asociadas: Delta Lake, Apache Iceberg, Apache Hudi

---

## 6. Formatos de almacenamento

A elección do formato de datos é clave para### o rendemento e a compatibilidade:

| Formato | Tipo | Características |
|--------|------|-------------------|
| CSV    | Texto plano | Sinxelo, universal, pouco eficiente |
| JSON   | Texto plano | Lexible, semi-estruturado, verbose |
| Parquet| Columnar | Eficiente para lectura selectiva e compresión |
| Avro   | Binario | Compacto, schema embebido |

Parquet adoita ser o formato preferido en arquitecturas Big Data modernas.

---
## 7. Modelos de organización de datos

### Arquitectura Medallion (Bronze - Silver - Gold)

Un enfoque progresivo para xestionar e almacenar datos con diferentes niveis de calidade:
![Arquitectura medallion](images/medallion.png)
- **Bronze**: datos en bruto, sen procesar. Ex: CSVs cargados de APIs ou logs.
- **Silver**: datos limpos, validados, estruturados. Ex: Parquet con tipos definidos e nulos eliminados.
- **Gold**: datos preparados para BI, agregados, resumidos e optimizados para consulta.

Esta arquitectura favorece a calidade de datos e a trazabilidade. É especialmente útil en arquitecturas tipo Lakehouse.

### Arquitectura Lambda
Divide o sistema nas seguintes capas.
- **Batch layer**: Procesa datos históricos en grandes lotes.
- **Speed layer**: Procesa datos en tempo real con latencia baxa.
- **Serving layer**: Combina os resultados das dúas capas anteriores para ofrecer respostas aos usuarios
#### Vantaxes:
- Permite combinar precisión (batch) e velocidade (streaming).
- Ben adaptado para escenarios onde a baixa latencia é crítica (tempo real).
#### Desvantaxes:
- Complexidade: Dúas lóxicas de procesamento distintas.
- Alto custo de mantemento.
Emprégase  en sistemas OLAP, motores como *Apache Storm*, *Spark Streaming*, etc.

### Arquitecutra Kappa
Semellante a *Lambda* pero fai todo o procesamento a través de streaming.
#### Vantaxes:
- Simplicidade: Unha soa base para procesamento en tempo real.
- Escalable e elástica.
#### Desvantaxes:
- Só para procesamento en streaming.
- Require ferramentas potentes (*kafka+flink/spark*).
Emprégase en escenarios orientados a eventos (*event-driven*).

### Arquitectura Data Lakehouse
Unifica o enfoque de *Data warehouse* e *Data Lake*. Pode ou non seguir a arquitectura *Medallion*.
#### Vantaxes:
- Flexibilidade.
- Integración nativa con ferramentas de *BI* e *SQL*.
#### Desvantaxes:
- Menos explícita que *Medallion*.
Emprégase en sistemas baseados en *Delta Lake*, *Apache Iceberg* ou *Apache Hudi*.

### Arquitectura ELT 
En contraposición a *ETL* (extracción-transformación-carga), intercambia as dúas últimas fases: extracción - carga - transformación.
- Extracción: datos brutos no data lake.
- Carga: directamente ao destino (Data Warehouse/Lakehouse).
- Transformación: no destino empregando *DBT* ou *SQL*.

![Arquitectura ELT](images/elt.png)
#### Vantaxes:
- Máis transparente e rastrexable.
- Boa integración con ferramentas modernas.
#### Desvantaxes:
- Precisa almacéns de datos potentes.
Non moi eficaz con datos desestruturados.

---

## 8. Consideracións finais

- Separar almacenamento de datos brutos e tratados facilita o mantemento e control da calidade.
- A redundancia (réplicas) e o formato columar (como Parquet) melloran a eficiencia.
- Ferramentas como Spark, Hive ou Airflow integran ben tanto con HDFS como con MinIO.
- Coa chegada dos Lakehouses, é cada vez máis común tratar todos os tipos de datos (estruturados e non) nun só sistema escalable.

---

Estes apuntamentos serven como base para comprender a infraestrutura de datos antes de abordar a inxesta, o procesamento e a analítica avanzada.

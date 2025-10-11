---
layout: default
title: Introdución ao Big Data
---

# Introdución **Big Data**

---
## 1. Que é o Big Data?

**Big Data** refírese ao conxunto de datos que, polo seu **volume**, **variedade** e **velocidade** de xeración, superan as capacidades das ferramentas tradicionais de procesamento e análise.

Non se trata só da cantidade de datos, senón de como xestionalos, procesalos e extraer valor deles.

### As 5 V's do Big Data
Aínda que ao principio eran 3, agora considérase que estas son as 5 características fundamentais que definen un sistema *Big Data*:

| V              | Significado                                                                          |
| -------------- | ------------------------------------------------------------------------------------ |
| **Volume**     | Grandes cantidades de datos (terabytes, petabytes…).                                 |
| **Velocidade** | Xeración e procesamento de datos en tempo real ou case real.                         |
| **Variedade**  | Datos estruturados, semiestruturados (XML, JSON) e non estruturados (vídeo, texto…). |
| **Veracidade** | Fiabilidade, calidade e consistencia dos datos.                                      |
| **Valor**      | Capacidade de obter coñecemento útil ou predicións a partir dos datos.               |
| **Viabilidade** | Capacidade da organización para realizar un uso eficaz dos datos |
| **Visualización**      | Capacidade de representar os datos visualmente mediante gráficos ou indicaderos (KPI) para que sean lexibles e accesibles.               |

### A orixe do Big Data
Estas son as razóns que impulsaron o nacemento do **Big Data**:
- A dixitalización masiva (móbiles, sensores, redes sociais...).
- A necesidade de tomar decisións baseadas en datos (data-driven).
- A aparición de tecnoloxías "baratas" de almacenamento e procesamento distribuído.

### Arquitectura típica dun sistema Big Data
1. **Inxesta de datos**: Captación de datos dende múltiples fontes: webes, sensores, APIs...).
2. **Almacenamento**: Gardar datos nun formato escalable, normalmente distribuído (*HDFS*, *S3*, *Delta Lake*...).
3. **Procesamento**: Limpeza, transformación e análise de datos (*Apache Spark*, *Apache Beam*, *Apache Flink*...).
4. **Visualización**: Creación de informes e dashboards (*Power BI*, *Tableau*, *Superset*...).
5. **Gobernanza/orquestración**: Control de acceso, seguridade, calidade de datos (*Apache Airflow*, *Apache Nifi*, *Ozie*...).
![Arquitectura](images/arquitectura-bd.png)
### Retos do Big Data
A día de hoxe, estes son os principais retos do **Big Data**:
- Escalabilidade e rendemento.
- Garantir a calidade dos datos.
- Protexer a privacidade e a seguridade.
- Xestionar a complexidade das infraestruturas.
- Interpretar correctamente os resultados.
![Os retos do Big Data](images/big-data-cloud-based-solution.webp)
## 2. Contexto: profesións e perfís relacionados cos datos

A xestión de grandes volumes de datos non é un fin en si mesmo, senón un medio para obter información, tomar decisións mellores ou automatizar procesos. Neste contexto existen distintos **perfís profesionais** que traballan con datos:

### Analista de datos (*Data Analyst*)
- Usa ferramentas como Excel, SQL ou Power BI para responder preguntas de negocio.
- Explora, visualiza e comunica resultados.
- Traballa sobre datos xa limpos e preparados.

### Enxeñeiro/a de datos (*Data Engineer*)
- Constrúe a infraestrutura para que os datos poidan ser almacenados, procesados e consumidos.
- Usa ferramentas como Spark, Kafka, Airflow, HDFS...
- Encárgase da calidade, integridade e dispoñibilidade dos datos.

### Científico/a de datos (*Data Scientist*)
- Aplica modelos estatísticos ou de *machine learning* para descubrir patróns ou facer predicións.
- Usa Python, R, notebooks, bibliotecas de IA.
- Precisa datos preparados previamente pola enxeñaría de datos.

### Arquitecto de datos
- Define a infraestrutura necesaria para a xestión de grandes volumes de datos.
- Encárgase de definir a estratexia respecto á escalabilidade, gobernanza, linaxe e seguridade dos datos.

> Neste módulo imos traballar sobre todo as tarefas propias da **enxeñaría de datos**, pero tocando tamén parte do traballo analítico.

---
## 3. Enxeñería de datos
É a base sobre a que se sustentan a ciencia e a analítica de datos en produción.

A **enxeñaría de datos** trata do movemento, manipulación e xestión dos datos. Se buscamos unha definición máis completa, podemos dicir que se centra no desenvolvemento, implementación e mantemento dos sistemas e procesos que recuperan os datos en cru e producen información consistente e de alta calidade que dá soporte aos diferentes casos de uso, como poden ser a analítica de datos ou a aprendizaxe automática (*machine learning*).

**Enxeñeiro de datos**: Encargado de xestionar o cliclo de vida, recuperando os datos dende os sistemas de orixen e servíndoos a futuros consumidores, que poden ser científicos de datos, ferramentas de visualización, modelos de IA, etc.

Poden distinguirse os seguintes roles:
### Roles de enxeñaría de datos

- **Enxeñeiro/a de datos de produto**: encargado/a de instalar, configurar e manter os produtos do equipo de enxeñaría de datos, como poden ser **Airflow, Kafka ou Spark**.  

- **Enxeñeiro/a de datos de *pipeline***: encargado/a de traballar co fluxo de datos, con coñecementos de **Python, SQL, Spark**, así como de traballar con **lagos de datos ou data lakehouse**.  

- **Enxeñeiro/a de datos de BI**: con coñecementos de **SQL** e ferramentas de visualización como **Power BI ou Tableau**, para mostrar analíticas.  

### Cliclo de vida dos datos
1. **Xeración**: quen, onde, como e cando se crean os datos: ficheiros en diferentes formatos, APIs, bases de datos OLTP, sistemas OLAP, etc.
2. **Almacenamento**: Estruturado e non estruturado. Verase con detalle próximamente.
3. **Consumo**: O destino dos datos adoita ser análise, aprendizaxe máquina ou ETLs inversas.
![ciclo de vida dos datos](images/05de-ciclo-vida.png)

### Almacenamento
Ainda que xa profundizaremos noutras unidades, antes de comezar é necesario ter unha idea das diferentes capas que se teñen en contar no almacenamento masivo de datos:
- **Capa física**: Refírese aos medios de almacenamento: HDD, SDD, RAM, etc.
- **Sistemas de almacenamento**: Capa que se sitúa por riba da física. Algúns exemplos son os sistemas de ficheiros distribuídos (HDFS), Os Sistemas de xestión de bases de datos relacionais (Posgres, Mysql, etc.), sistemas de xestión de bases de datos NoSQL (MongoDB, Cassandra, ElasticSearch,etc.), almacenamento de obxectos (S3), etc.
- **Abstración de almacenamento**: Capa superior que aporta organización dos datos mecanismos de consulta, características especiais (*Time Travel*, etc.). Poden estar enfocadas a datos estruturados (*Data Warehouse*, *Olap*, etc.), semiestruturados (*DataLake*, *Delta Lake*) ou ambos (*LakeHouse*).
![ciclo de vida dos datos](images/05storage.png)

Poden distinguirse as seguintes fases:
#### Inxesta
Consiste en mover datos dende unha fonte ao almacenamento. Conceptos fundamentais:
- **pipeline** de datos.
- Estratexias de inxesta: *push*, *pull* ou *poll*.
- Inxesta baseada en *ventás temporais* ou *cantidade*.
- Inxesta mediante *snapshots* ou *incremental*.
- Inxesta dende ficheiros.
- *ETL* vs *ELT*.

#### Transformación

A fase de **transformación** inclúe:

- **Consultas**  
  - Analizar plans de execución e coñecer o optimizador de cada motor.  
  - Evitar `joins` complexos, mellor persistir resultados intermedios en táboas.  
  - Usar **CTEs** en vez de subconsultas.  
  - Evitar escaneos completos de táboas/columnas → filtrar, limitar e usar índices.  
  - En datos en *streaming*, coñecer tipos de fiestras (ventás) e marcas de auga.  

- **Modelado de datos**  
  - Introducir a lóxica de negocio mediante modelos conceptuais, lóxicos e físicos.  
  - Normalización/denormalización segundo sexa preciso.  
  - Coñecer modelado multidimensional:  
    - **Kimball**: esquema en estrela (táboa de feitos + dimensións).  
    - **Data Vault**: *hubs*, enlaces e satélites.  

- **Transformacións**  
  - Unir resultados de consultas e modelos para xerar valor.  
  - Consultas complexas poden persistirse temporal ou permanentemente.  
  - Precísase un sistema de **orquestración**.  
  - Ferramentas: SQL (Hive, Spark SQL) ou código (Pandas, PySpark).  
  - Coñecer limitacións de sistemas *insert-only* (reescritura de ficheiros, truncado e recarga, rexistros con versión temporal, etc.).  
  - Persistencia mediante táboas, vistas ou vistas materializadas.  
  - Posible uso de **consultas federadas** (combinar datos de varios sistemas).  


---

#### Serving (Presentación dos datos)

Unha vez transformados, os datos preséntanse para **analítica** ou **ML**, asegurando sempre a súa validación.

- **Analítica de datos**  
  - **De negocio**: uso de datos históricos e actuais para tomar decisións (dashboards, KPIs).  
  - **Operacional**: reacción inmediata con datos recentes.  
  - **Embebida**: analítica integrada en aplicacións (ex. monitorización en AWS EC2).  

- **Machine Learning**  
  - Calidade dos datos = calidade do modelo.  
  - Conceptos clave:  
    - Supervisado / non supervisado / semisupervisado.  
    - Clasificación vs regresión.  
    - Tratamento de series temporais.  
    - Modelos clásicos (regresión, árbores, SVM) vs deep learning.  
    - AutoML vs deseño manual de modelos.  
  - Todos os datos deben converterse a numéricos: *feature engineering*, codificación categórica, embeddings.  
  - Elección de hardware: CPU, GPU, local ou cloud segundo problema e tamaño do dataset.  

- **Formas de entrega de datos a ML**  
  - Ficheiros (dependendo do uso e acceso).  
  - Bases de datos (*warehouses*, *lakehouses*).  
  - Sistemas de *streaming*.  
  - Consultas federadas (con menor rendemento).  
  - **Jupyter Notebooks**: experimentación local ou en cloud, logo migración a scripts.  
  - **Reverse ETL**: reintroducir datos transformados na fonte de orixe (ex. inserir audios etiquetados nun proxecto).  

### Destrezas e ámbitos de apoio na enxeñaría de datos

Ademais das fases principais, existen unha serie de ámbitos transversais (*undercurrents*) que sustentan a enxeñaría de datos:

#### Seguridade
- Principio de menor privilexio (PoLP) para persoas e sistemas.  
- Control de tempos de acceso.  
- Protección da visibilidade mediante encriptación, enmascaramento, ofuscación ou sistemas de acceso robustos.  
- A seguridade é un ámbito amplo en si mesmo → conexión coa ciberseguridade.  

#### Xestión do dato
- **Gobernanza**: asegurar calidade, integridade, seguridade e usabilidade.  
- **Descubribilidade**: acceso, orixe, relacións e significado dos datos mediante metadatos.  
- **Modelado**: relacional, NoSQL, JSON (APIs REST), GraphQL.  
- **Linaxe**: trazar o ciclo de vida do dato.  
- **Integración e interoperabilidade**: orquestración de procesos.  
- **Ética e privacidade**: protección de datos sensibles e cumprimento legal.  

#### DataOps
Conxunto de técnicas e patróns para entregar valor rápido con datos de alta calidade.  
Elementos principais:
- **Automatización**: despregues (ex. DAGs en Airflow, dependencias en Python, CI/CD).  
- **Monitorización e observabilidade**: detección temperá de problemas.  
- **Xestión de incidentes**: resolución automática ou rollback a versión estable.  

#### Arquitectura e orquestración
- Arquitecturas: microservizos, big data, IoT, etc.  
- **Orquestración**: coordinar inxestión, transformación e almacenamento (ex. Airflow).  

#### Enxeñaría do software
- Técnicas de **testing**.  
- Uso de funcións xanela para datos en *streaming*.  
- Nocións de **DevOps**: infraestrutura como código.  
- Control de versións e boas prácticas de desenvolvemento.  

---

### Obxectivos dun enxeñeiro/a de datos
- Optimizar o **retorno do investimento (ROI)**.  
- Reducir **custos** (financeiros e de oportunidade).  
- Minimizar **riscos** (seguridade, calidade dos datos).  
- Maximizar o **valor e utilidade dos datos**.  

### Tecnoloxías esenciais para un/ha enxeñeiro/a de datos

Ademais das **soft skills**, un bo enxeñeiro/a de datos debería ter destreza, como mínimo, nas seguintes tecnoloxías:

- **Linux** (especialmente bash) → para operacións no sistema operativo.  
- **SSH e redes** → acceso a máquinas remotas.  
- **APIs REST** → inxestión de datos de fontes externas.  
- **Git, GitHub e GitHub Actions** → coñecemento do ciclo de **CI/CD**.  
- **Docker** → xestión de contedores para despregar ferramentas.  
- **Jupyter Notebooks** → desenvolver procesos de extracción, transformación e carga de datos (ETL).  
- **SQL** → lingua franca para a transformación e analítica de datos.  
- **Linguaxes de programación**:  
  - **Python**: fundamental para scripts e programas de soporte.  
  - **Scala**: relevante nalgúns ámbitos da enxeñaría de datos.  


## 4. Tecnoloxías que imos empregar (e alternativas por categoría)

### Almacenamento distribuído
**Ferramentas do curso**:
- **HDFS** → Sistema de ficheiros distribuído de Hadoop, deseñado para traballar con grandes volumes de datos e tolerancia a fallos. Úsase como base en moitas arquitecturas Big Data.
- **MinIO** → Solución de almacenamento obxectual, lixeira e compatible coa API de Amazon S3. Ideal para despregues en contornas locais ou privadas.

**Outras alternativas**:
- **Amazon S3**, **Azure Data Lake**, **Google Cloud Storage** → opcións na nube, altamente escalables.
- **Ceph** → almacenamento distribuído de código aberto, usado en contornas híbridas.

---

### Procesamento de datos
**Ferramenta principal**:
- **Apache Spark (PySpark)** → Motor de procesamento distribuído. Permite traballar en **batch**, **streaming** en tempo real, consultas SQL, machine learning (MLlib) e gráficos (GraphX).

**Alternativas coñecidas**:
- **Apache Flink** → moi potente en *streaming* de baixa latencia.
- **Apache Beam** → modelo unificado para batch e streaming (execútase sobre Spark, Flink, Dataflow).
- **Hive, Presto/Trino** → SQL distribuído sobre grandes volumes de datos.
- **Dask** → alternativa en Python para computación distribuída, máis sinxela ca Spark en certos escenarios.

---

### Inxestión e fluxo de datos
**Ferramentas empregadas**:
- **Apache NiFi** → Interface gráfica para deseñar fluxos de datos, con conectores a múltiples sistemas.
- **Apache Airflow** → Orquestrador e planificador de tarefas. Moi usado para *ETL pipelines* e integración con Python.

**Alternativas populares**:
- **Talend** e **StreamSets** → ferramentas gráficas comerciais e open source de ETL.
- **Dagster** → alternativa moderna a Airflow, centrada en *data pipelines* robustos.
- **Azure Data Factory**, **AWS Glue** → servizos de nube para integración e orquestración.

---

### Comunicación entre sistemas
**Ferramenta opcional e avanzada**:
- **Apache Kafka** → Plataforma distribuída de *streaming*. Permite integración en arquitecturas orientadas a eventos, con alta escalabilidade e tolerancia a fallos.

**Outras opcións**:
- **RabbitMQ** → cola de mensaxes lixeira, boa para microservizos.
- **Apache Pulsar** → alternativa moderna a Kafka, con mellor integración en multi-cluster.
- **MQTT** → protocolo moi usado en IoT por ser lixeiro.
- **Azure EventHub**, **Google Pub/Sub** → solucións na nube para *event streaming*.

---

### Visualización e exploración de datos
**Ferramentas que imos usar**:
- **Apache Superset** / **Metabase** → Plataformas open source para crear **dashboards interactivos** e consultas SQL.
- **JupyterLab** → Entorno flexible en Python, útil para análise exploratoria, estatística e visualización con Pandas, Matplotlib, Seaborn, etc.

**Alternativas comerciais**:
- **Power BI**, **Tableau**, **Looker**, **QuickSight** → Ferramentas de Business Intelligence con máis soporte empresarial, integración directa con múltiples fontes de datos e opcións avanzadas de visualización.

---

---

## 5. Dúas opcións arquitectónicas: solución comercial ou personalizada

| Opción                           | Características                                                  | Vantaxes                                                | Riscos ou custos                                    |
|----------------------------------|------------------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|
| **Solución comercial integral** | Plataforma unificada na nube con servizos integrados             | Sinxeleza, rapidez de posta en marcha, rendemento alto | Custos por uso, dependencia do provedor             |
| **Arquitectura personalizada**  | Montaxe combinada con ferramentas libres e autoaloxadas          | Control total, aprendizaxe profunda, custo baixo       | Complexidade inicial, mantemento                   |

> 🔧 No curso imos construír unha **arquitectura personalizada**, que permita ao alumnado:
> - Entender cada compoñente tecnolóxico.
> - Adaptarse máis tarde a plataformas comerciais como Databricks, Fabric ou Confluent.

---

## 6. Solucións comerciais end-to-end: descrición e relación coas tecnoloxías

### Databricks
- Plataforma cloud creada polos fundadores de Apache Spark.
- Baseada nun modelo *Lakehouse*: datos en bruto e refinados no mesmo sistema.
- Incorpora: Spark, Delta Lake, clusters autoxestionados, notebooks, MLflow, orquestración.
- **Tecnoloxías similares vistas no curso**: Spark, MinIO/HDFS, Airflow, Superset.

---

### Microsoft Fabric
- Plataforma unificada de datos dentro do ecosistema Microsoft 365.
- Inclúe: OneLake, Data Factory, Power BI, Notebooks, Spark engine.
- **Tecnoloxías similares vistas no curso**: MinIO, NiFi, Airflow, Spark, Superset.

---

### Google Cloud Platform
- Ecosistema baseado en BigQuery (DWH), Dataflow (Apache Beam), Looker (BI).
- Incorpora tamén Pub/Sub (streaming).
- **Tecnoloxías similares vistas no curso**: Spark (substituído por Beam), Kafka, Superset, Airflow.

---

### Amazon Web Services
- Plataforma modular: S3 (almacenamento), Glue (ETL), Redshift (DWH), QuickSight (BI).
- Inclúe MSK (Kafka xestionado).
- **Tecnoloxías similares vistas no curso**: MinIO, NiFi, Airflow, Spark, Kafka, Superset.

---

### Confluent Platform
- Versión empresarial de Apache Kafka.
- Incorpora: Kafka Connect, KSQL, Schema Registry, Control Center.
- Foco en arquitecturas orientadas a eventos en tempo real.
- **Tecnoloxías similares vistas no curso**: Kafka, NiFi, Airflow.

---

### Táboa comparativa

| Plataforma        | Almacenamento | Procesamento | Inxestión / ETL      | Visualización BI | Streaming / eventos |
|------------------|----------------|--------------|-----------------------|------------------|----------------------|
| Databricks       | ADLS / S3      | ✔ Spark      | ✔ Pipelines propios   | ✔ Dashboards     | ✔ Kafka integrado    |
| MS Fabric        | OneLake        | ✔ Spark      | ✔ Data Factory        | ✔ Power BI       | EventHub (parcial)   |
| GCP              | GCS            | ✔ Beam       | ✔ Dataflow            | ✔ Looker         | ✔ Pub/Sub            |
| AWS              | S3             | ✔ Spark      | ✔ Glue                | ✔ QuickSight     | ✔ MSK (Kafka)        |
| Confluent        | Externo (S3…)  | ✖            | ✔ Kafka Connect       | Opcional         | ✔ Kafka completo     |

---

## 7. Ferramentas e contidos que se van desenvolver no curso

| Categoría              | Ferramentas ou contidos incluídos                          | Obxectivo no curso                                                 |
|------------------------|-------------------------------------------------------------|---------------------------------------------------------------------|
| Almacenamento          | HDFS, MinIO                                                  | Comprender almacenamento distribuído e obxectual                   |
| Procesamento batch     | Apache Spark + PySpark                                       | ETL e análises distribuídas con RDDs e DataFrames                  |
| Procesamento streaming | Spark Structured Streaming + fontes reais (Kafka ou APIs)   | Análise de datos en tempo real                                     |
| Inxestión / ETL        | Apache NiFi, Airflow                                         | Automatización de extracción e carga de datos                     |
| Comunicación           | Apache Kafka (teoría e práctica opcional)                   | Sistemas orientados a eventos, logs distribuídos                   |
| Visualización          | Superset / Metabase, JupyterLab                             | Dashboards e análise exploratoria                                  |
| Orquestración          | Airflow, NiFi                                                | Dependencias, planificación e control de execución de pipelines    |
| BI / análise final     | Power BI (observación), Superset (uso práctico)              | Comparativa real entre alternativas libres e comerciais             |

## 8. Glosario de termos
### Almacenamento
- **Data lake**: Repositorio de almacenamento masivo onde se gardan datos en bruto en calquera formato (non estruturados e semi-estruturados).
- **Data Warehouse**: Almacén de datos estruturados, deseñado para a análise e a toma de decisións. Habitualmente con topoloxía en estrela.
- **Delta Lake**: Capa transaccional ACID sobre un data lake que garante calidade, consistencia e control
- **Delta Table**:	Táboa en formato Delta con capacidades avanzadas de consulta e modificación de datos
### Procesamento de datos
- **Batch Processing**: Procesamento de datos en bloques ou lotes, con latencia alta pero bo rendemento para grandes volumes.
- **Streaming Processing**: Procesamento en tempo real de fluxos de datos continuamente xerados.
- **ETL (Extract, Transform, Load)**: Proceso clásico de extracción, transformación e carga de datos.
- **ELT (Extract, Load, Transform)**: Variante moderna onde os datos se cargan primeiro e logo se transforman no destino.
### Análise e ciencia de datos
- **DataFrame**: Estrutura de datos tabular usada en Spark e Pandas.
- **Data Profiling**: Técnica para analizar a estrutura, calidade e estatísticas dos datos.
- **Feature Engineering**: Proceso de transformación de datos en características relevantes para modelos de aprendizaxe automática.
- **Model Deployment**: Fase de despregue dun modelo para que poida ser usado en produción.
### Visualización e ciencia de datos
- **Dashboard**: Panel visual que presenta indicadores clave e métricas en tempo real.
- **KPIs (Key Performance Indicators)**: Métricas críticas que indican o rendemento dos procesos de negocio.
### Calidade, monitorización e seguridade
- **Data Lineage**: Seguimento da orixe e transformación dos datos ao longo do seu ciclo de vida.
- **Observabilidade de datos**: Capacidade de rastrear, monitorizar e alertar sobre problemas nos fluxos de datos.
- **Data Governance**: Políticas e prácticas para asegurar o uso responsable, seguro e legal dos datos.
- **DLP (Data Loss Prevention)**: Conxunto de ferramentas para evitar a fuga de información sensible.
### Arquitectura
- **Lambda Architecture**: Modelo que combina procesamento batch e streaming para unha análise máis completa.
- **Kappa Architecture**: Variante que usa só fluxos de datos en tempo real, simplificando o deseño.
- **Medallion Architecture**: Arquitectura por capas (bronze, silver, gold) para mellorar a calidade dos datos progresivamente.
### Outros conceptos
- **Metadata**: Datos que describen outros datos (estructura, tipo, fonte...).
- **Schema Evolution**: Capacidade dun sistema para adaptar cambios no esquema dos datos.
- **Partitioning**: Técnica para dividir grandes conxuntos de datos en subconxuntos máis pequenos para mellorar o rendemento.
- **Shuffling**: Redistribución de datos entre tarefas no proceso de agrupación ou combinación en Spark.

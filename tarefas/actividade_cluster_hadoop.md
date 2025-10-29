# Actividade: Constrúe o teu propio clúster Hadoop con Docker

## Obxectivo
A partir do repositorio base **simple-hadoop-cluster** (https://github.com/adbgonzalez/simple-hadoop-cluster), que inclúe varios ficheiros incompletos, debes **construír unha imaxe funcional de Hadoop** e despregar un pequeno **clúster pseudo-distribuído** con `docker-compose`.  
Terás que completar as partes marcadas con `#TODO` para que o sistema funcione correctamente.


---

## 1. Dockerfile

O ficheiro `Dockerfile` describe como se constrúe a imaxe de Hadoop.  
Contén varios bloques incompletos que debes completar.

### Variables de entorno
Define as variables necesarias para que Hadoop e Java funcionen correctamente.  
Inclúe polo menos:
- A ruta de instalación de Java (`JAVA_HOME`)
- O directorio de Hadoop (`HADOOP_HOME`)
- O directorio de configuración (`HADOOP_CONF_DIR`)
- A variable `PATH` actualizada para incluír os binarios de Hadoop

### Instalación de paquetes
Engade unha instrución `RUN` que instale os paquetes necesarios para Hadoop.  
Debes instalar:
- `openjdk-11-jdk-headless` (versión sen interface gráfica)
- `openssh-client` e `openssh-server` (para comunicación entre nodos)
- `rsync`, `wget`, `curl`, `net-tools` e `ca-certificates`

Podes empregar `apt-get install -y --no-install-recommends` e lembrar limpar a caché de paquetes ao final.

### Directorio de traballo
Especifica que o usuario traballará dentro do directorio `/home/hadoop`.

### Descarga e instalación de Hadoop
Engade as instrucións necesarias para:
1. Descargar o ficheiro `.tar.gz` da versión indicada de Hadoop (usando a URL do comentario).
2. Descomprimilo en `/usr/local`.
3. Renomear o cartafol resultante como `/usr/local/hadoop`.
4. Cambiar o dono dos ficheiros ao usuario `hadoop:hadoop`.

### Cambio de usuario
Antes de configurar o SSH e executar Hadoop, cambia ao usuario `hadoop`.  
Lembra que non debe facerse como root.

### Exposición de portos
Expón os portos que necesitarán os servizos de Hadoop.  
Debes incluír os portos principais de:
- SSH (22)
- HDFS (por exemplo, 9870 para a UI do NameNode e 8020 para o servizo RPC)
- YARN (por exemplo, 8088 para o ResourceManager e 8042 para o NodeManager)

---

## 2. docker-compose.yml

Este ficheiro define os contedores que formarán o clúster.  
Tes que completar os apartados marcados como `#TODO`.

### Imaxe dos servizos
Substitúe `image: hadoop` pola imaxe que constrúas a partir do `Dockerfile`.  
Emprega un nome e etiqueta identificativos (por exemplo, `myhadoop:3.4.2`).

### Configuración do namenode
- Engade un `container_name` e un `hostname` descritivos.
- Expón os portos necesarios para acceder á interface web e ao servizo RPC.
- Configura un volume persistente para `/home/hadoop/namenode`.
- Monta o script `start-dfs.sh` no contedor.
- Verifica que emprega o comando indicado no ficheiro.

### Configuración do datanode
- Engade un `container_name` e `hostname` coherentes co NameNode.
- Indica que depende do servizo `namenode`.
- Crea un volume persistente para `/home/hadoop/datanode`.
- Comproba que o comando de execución é `hdfs datanode`.

### Configuración do resourcemanager
- Engade `container_name` e `hostname`.
- Expón o porto da interface web de YARN (8088).
- Establece a dependencia do `namenode`.
- Mantén o comando `yarn resourcemanager`.

### Configuración do nodemanager
- Engade `container_name` e `hostname`.
- Usa a mesma imaxe de Hadoop.
- Indica que depende do `resourcemanager`.
- Mantén o comando `yarn nodemanager`.

---

## 3. Ficheiros de configuración (cartafol `conf/`)

Tes que editar os catro ficheiros XML e engadir as propiedades necesarias para un clúster funcional.

### core-site.xml
Define o sistema de ficheiros por defecto de Hadoop.  
Debe apuntar ao NameNode do clúster mediante a propiedade `fs.defaultFS`.

### hdfs-site.xml
Configura:
- O número de réplicas (`dfs.replication`), que debe ser 1 neste caso.
- As rutas locais para o NameNode e o DataNode (`dfs.namenode.name.dir` e `dfs.datanode.data.dir`).

### mapred-site.xml
Indica o framework de execución para MapReduce.  
Debe especificar que se executará sobre YARN.

### yarn-site.xml
Engade a propiedade que indica o `hostname` do ResourceManager.  
O classpath principal xa está incluído no ficheiro base.

---

## 4. Execución do clúster

Unha vez completados todos os ficheiros:

1. Constrúe a imaxe:
   ```bash
   docker build -t nome_da_tua_imaxe:versión .
   ```

2. Desprega o clúster:
   ```bash
   docker-compose up -d
   ```

3. Comproba o funcionamento:
   - Interface web do NameNode: `http://localhost:9870`
   - Interface web do ResourceManager: `http://localhost:8088`

4. Proba algúns comandos básicos dentro do contedor do NameNode:
   ```bash
   hdfs dfs -mkdir /user
   hdfs dfs -ls /
   ```

---

## 5. Autoavaliación

- Completaches correctamente todos os apartados `#TODO`.
- O clúster arrinca sen erros e os servizos están accesibles.
- Comprendes a función de cada servizo e dos principais ficheiros de configuración de Hadoop.
- Sabes verificar o funcionamento de HDFS e YARN.
- Hai persistencia. Para comprobalo seguiremos os seguintes pasos:
  - Crea esta estrutura de directorios e sube un ficheiro.
```bash
docker exec -it namenode bash
hdfs dfs -mkdir /user/
hdfs dfs -mkdir /user/hadoop
echo ola > ola.txt
hdfs dfs -put ola.txt
exit
```
  - Para o docker-compose e volve a arrincalo. Comproba que sigue existindo o arquivo co seguinte comando:
```bash
docker exec namenode hdfs dfs -ls
```
> A Tarefa considerarase completada se se cumple todo o espeficiado no apartado 5.

> **ENTREGAR**: ficheiro.zip co contido do directorio ou ligazón a repositorio en github.

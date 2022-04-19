# Actividad 5 - Conceptos y Comandos básicos del particionamiento  en bases de datos NoSQL
## Requerimientos No Funcionales
1. Escalamiento horizontal de datos.
2. Fragmentación de bases de datos.
3. La escritura se deben realizar desde un solo servidor.
4. El grupo de partición debe estar compuesto por servidores de configuración, enrutadores de consultas y shards.
5. El sistema debe emplear particionamiento basado en hash. 

## Comandos
|Comando|Función|
|--|--|
|md|Crea directorio en ruta especifica|
|--port|Indica el puerto al que se hace el llamado|
|--dbpath|Indica el directorio que contiene los datos|
|--configsvr|Especifica el servidor de configuración|
|--configdb|Asigna los enrutadores de consulta|
|--shardsvr|Crea un shard en una ruta especifica|

## Scripts
Creación de directorios:
<pre><code>
md C:\data\repl\n1 
md C:\data\repl\n2 
md C:\data\repl\n3 
md C:\data\repl\n4 
md C:\data\repl\n5 
md C:\data\repl\n6 

md C:\data\config\n1 
md C:\data\config\n2 
md C:\data\config\n3 
</code></pre>

Creación de nodos de conjunto de replicas: 
<pre><code>
mongod --port 27017 --dbpath \data\repl\n1 --replSet rs0
mongod --port 27018 --dbpath \data\repl\n2 --replSet rs0
mongod --port 27019 --dbpath \data\repl\n3 --replSet rs0
</code></pre>

Adición de nodos secundarios desde el puerto 27017: 
<pre><code>
rs.add("192.168.0.18:27018")
rs.add("192.168.0.18:27019")
</code></pre>

Llamado a base de datos:
<pre><code>
use myfootballnine 
</code></pre>

Activación de servidores:
<pre><code>
mongod --port 27022 --dbpath \data\config\n1 --configsvr --bind_ip 192.168.0.18
mongod --port 27023 --dbpath \data\config\n2 --configsvr --bind_ip 192.168.0.18
mongod --port 27024 --dbpath \data\config\n3 --configsvr --bind_ip 192.168.0.18
</code></pre>

Activación de enrutador:
<pre><code>
mongos --configdb 192.168.0.18:27022,192.168.0.18:27023,192.168.0.18:27024 --port  27021 --bind_ip 192.168.0.18
</code></pre>

Asignación de réplica inicial como shard:
<pre><code>
mongo --port 27021 --host 192.168.0.18
</code></pre>

Creación de segundo shard:
<pre><code>
mongod --port 27013 --dbpath \data\repl\n4 --replSet rs1
mongo --port 27013
rs.initiate()
mongod --port 27014 --dbpath \data\repl\n5 --replSet rs1
mongod --port 27015 --dbpath \data\repl\n6 --replSet rs1
</code></pre>

Adición de nodos secundarios desde el puerto 27013:
<pre><code>
rs.add("192.168.0.18:27014")
rs.add("192.168.0.18:27015")
</code></pre>

Adición de réplica como shard:
<pre><code>
sh.addShard("rs1\192.168.0.18:27013,192.168.0.18:27014,192.168.0.18:27015")
sh.status()
</code></pre>

Distribución de colección desde enrutador:
<pre><code>
use myfootballnine
sh.enambleSharding("myfootballnine")
db.ensayo.createIndex({"_id":1})
sh.shardCollection("myfootballnine.ensayo",{"_id:1"}){ "collectionsharded" : "myfootballnine.ensayo", "ok" : 1 }
</code></pre>

_ _ _
# Actividad 6 : Pruebas de particionamiento de bases de datos NoSQL
## Casos de Prueba de Replicación de Bases de Datos NoSQL
|Requisitos|Prueba|Resultado Esperado|
|--|--|--|
|Escalamiento horizontal de datos.|Escalamiento horizontal de datos.|Muestra la distribución de documentos en los shards implementados.|
|Fragmentación de bases de datos.|Consulta sobre distribución con el comando getShardDsitribution()|Indica el espacio en memoria asignado a cada shard y el peso que contienen.|
|La escritura se deben realizar desde un solo servidor.|Se intenta escribir sobre un nodo no primario al azar, luego se comprueba desde el primario.|No es posible escribir desde un nodo secundarios, solo el nodo primario tiene permiso de escritura.|
|El grupo de partición debe estar compuesto por servidores de configuración, enrutadores de consultas y shards.|Se verifica que los puertos asignados a los servidores de confirguración, enrutador y shards, se encuentren activos usando el comanto netstat -a|Se verifica que los puertos asignados a los servidores de confirguración, enrutador y shards, se encuentren activos usando el comanto netstat -a|
|El sistema debe emplear particionamiento basado en hash.|Con getShardDsitribution() se revisan los pesos de los chunks, se adicionan 3 documentos de forma sucesiva y se verifica posteriormente los pesos de los chunks.|En la segunda prueba debe aumentar los pesos de los chunk uniformemente.|

## Nota
La evidencia de la ejecución del código esta registrada en el documento pdf llamado "Actividad 6 - Pruebas de Particionamiento de Bases de Datos NoSQL - Holman Cruz.pdf"
Siguiendo el paso a paso del capítulo VIII Sharding, del libro introducción a las bases de datos nosql usando mongodb, logré llegar solo hasta el script que inicia el enrutador, probé con varios ajustes sobre la sintaxis, sin embargo, obtuve la misma respuesta “las conexiones del servidor de configuración reflejado no son compatibles”.

## Conclusiones
- El particionamiento en bases de datos no relacionales permite que las consultas puedan ser gestionadas por rango o por hash.
- La capacidad de los shards puede ser asignada editando los espacios dedicados a cada chunk.
- Al particionar bases de datos se reducen costos en recursos de máquinas, puesto que su escalamiento es horizontal e ilimitado.
- El almacenamiento de datos en particionamiento basado en rango es mejor organizado que el basado en hash, sin embargo, el basado en hash proporciona un mejor balanceo sobre el conjunto de shards.


## Referencias Bibliográficas
Sarasa, A. (2016). *Introducción a las bases de datos NoSQL usando MongoDB*.. Editorial UOC. [https://elibro.net/es/lc/biblioibero/titulos/58524]

MongoDB. (2008). *MongoDB Sharding*. Shardign [https://www.mongodb.com/docs/v4.0/sharding/]

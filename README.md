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


   
_ _ _
# Actividad 6 : Pruebas de particionamiento de bases de datos NoSQL


# Documentación EMR
En la siguiente documentación se plantea como montar un cluster EMR desde AWS, con una configuración sencilla para levantar los servicios. Los servicios que serán levantados son:

 - Cluster AWS EMR versión 3.6.1 con 1 master y 2 instancias.
 - Bucket S3 para dar persistencia a la información.
 - Servicios desplegados en el cluster:
	 - Hadoop 3.2.1
	 - JupyterHub 1.2.0
	 - Hive 3.1.2
	 - Sqoop 1.4.7
	 - Zeppelin 0.9.0
	 - Tez 0.9.2
	 - Hue 4.9.0
	 - JupyterEnterpriseGateway 2.1.0
	 - Spark 3.1.1
	 - Livy 3.1.1
	 - Livy 0.7.0
	 - HCatalog 3.1.2
---
### Requisitos

> Importante contar con todos los requisitos antes de proseguir con la creación del cluster, ya que podría suponer problemas durante la creación o a futuro.

1. Cuenta de AWS con creditos disponibles para levantar servicios.
2. Cuenta que disponga de permisos totales o parciales para acceder a los servicios EC2, S3 y EMR.
3. Bucket de S3 (preferiblemente sin archivos).
4. Par de llaves (se pueden crear desde EC2).

### Creación del Cluster

1. Nos dirigimos a AWS EMR y presionamos "Crear cluster".
2. Presionamos "Ir a las opciones avanzadas".
3. Seleccionamos la versión 6.3.1 de emr.
4. Marcamos todas las opciones de servicios que hemos dado en la primera parte del documento (sección `Servicios desplegados en el cluster`)
5. En la sección del JSON, agregamos el siguiente (remplazar `mi-bucket-de-s3-creada` por el nombre del bucket de S3 previamente creado):
```javascript
[
   {
      "Classification":"jupyter-s3-conf",
      "Properties":{
         "s3.persistence.enabled":"true",
         "s3.persistence.bucket":"<mi-bucket-de-s3-creada>"
      }
   }
]
```
6. Activar el uso de los metadatos. Este apartado debería quedar así:
![enter image description here](https://imgur.com/hSKS51k.png)
7. Elegir los tipos de nodos, (asegurarse que almenos sean de 16 GB), esta puede ser una configuración valida:
![enter image description here](https://imgur.com/JR64c7t.png)
8. En la sección de "Opciones de seguridad" seleccionar la key `EC2 key pair`.
9. Dejar el resto igual y darle "Continuar" hasta crear el cluster. 
10. El cluster tarda alrededor de 20 minutos en crearse. 

### Configuración de Permisos

1. En las configuraciones de Security Groups en EC2, dirigirse al SG creado por EMR para el nodo master y configurar los puertos `9443, 8890, 8888 y 22` para habilitar los accesos.
2. En EMR dirigirse y editar las excepciones agregando los puertos nombrados en el paso 1. El bloqueo del acceso publico debe dejarse en `On`.

### Revisión de Status

1. Nos dirigimos al servidor de DNS público, y podemos utilizar el puerto `8888` para acceder al entorno de Hue, el puerto `8890` para acceder al entorno Zeppelin y finalmente `9443` para acceder al entorno de JupyterHub.
2.  Alli podremos subir archivos desde Hue y realizar operaciones.
3. Si queremos subir un archivo desde SSH podemos acceder al nodo master desde las instrucciones de AWS y  ejecutar el siguiente comando: `hadoop fs -put /home/test/<file> /path/in/hdfs` 

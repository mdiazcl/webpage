# Taller de Análisis de Log
---
## Bienvenido estudiante!
Primero que todo, muchas gracias por querer participar de este Evento llamado [**Fraternity**](https://partyhack.cl/fraternity) de [Partyhack.cl](http://Partyhack.cl)! Tu apoyo es crucial e importante para la gran cruzada que tenemos por delante.

Para poder disfrutar y sacar el mayor provecho de este taller es necesario que vayas preparado, tengas tu equipo y, si te da el tiempo, haber estudiado algunas cosas previas para agilizar la parte teórica!

En esta guía prepararemos nuestro computador para hacer la parte práctica del taller. Así que bienvenido y si tienes cualquier duda me puedes ubicar en Telegram bajo el nick de [**@MdiazCL - Telegram**](https://t.me/mdiazcl) o preguntarme vía Twitter en [**@mdiazcl**](https://twitter.com/mdiazcl).

## Requerimientos de computador

Para este laboratorio vamos a utilizar un set de herramientas que nos permitirán almacenar, procesar y analizar datos, por lo que tu computador debe tener las capacidades para correr estas herramientas! Como mínimo te recomiendo contar con una máquina con las siguientes características.

**Requerimientos mínimos**

* **Procesador:** Procesador de 4 núcleos (x64) sobre 2.0ghz
* **Memoria Ram:** 8gb o más
* **Disco Duro:** 50gb (si usas VM usa espacio dinámico)
* **Software:** Java v1.8
* **Sistema Operativo:** Debian/Ubuntu

**Nota importante si vas a usar Windows**
Te aconsejo correr esto en una máquina virtual con Linux, no tengo experiencia corriendo las herramientas en entornos Windows. Según la página de [https://www.elastic.co/](https://www.elastic.co/) todo debería funcionar, sin embargo si tienes problemas, intentaré ayudarte! Pero no prometo nada.

**Si usarás otra distribución de Linux**
Si utilizarás alguna otra distribución de Linux, tan solo realiza los cambios de los comandosp por los que pertenecen a ese sistemas operativo. Los comandos que usaremos son bastante nativos por lo que no deberían haber muchos problemas.

## OVA para Virtualbox
---
Gracias al grandioso aporte de Felipe Castañeda tenemos disponible una OVA para Virtualbox con la máquina lista para el laboratorio! Si no puedes instalar las herramientas descarga este archivo e importalo a tu Virtualbox.

https://drive.google.com/file/d/1fOxPpfA6Yipidhwp-dh6IbbHOcCEXlH8/view

Si quiereas realizar la instalación tu mismo, continúa con esta guía.

## Instalación de herramientas
---
Para este laboratorio utilizaremos 5 herramientas!
* **ElasticSearch v6** - Una base de datos tipo Full-text Search
* **Kibana v6** - Un visualizador de datos
* **Logstash v6** - Un normalizador/preparador de datos
* **Navegador Web** (Firefox o Chrome) - Para navegar por el kibana
* **Java v8 Minimo**

Utilizaremos la versión community de estas herramientas! Por lo que no hay que adquirir ninguna licencia o algún requerimiento inicial. Verás que instalarla tomará minutos!

Primero que todo, chequiemos en una terminal que tenemos los requerimientos necesarios para ejecutar el Stack ELK (Elasticsearch, Logstash y Kibana).

En un terminal ejecuta lo siguiente:

```bash
java -version
```

La empresa [Elastic.co](http://Elastic.co) recomienda tener instalada la versión **1.8.0_131** o superior, ya que el programa reclamará si no tienes la versión suficiente. Si no la tienes instalada te recomienda descargarla e instalarla [directamente del sitio de Java](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html).

Una vez que la versión de java está comprobada y lista, descarguemos los ejecutables directamente desde la página de Elastic.


## Instalación de OpenJDK 8**
---
**(Gracias Felipe Castañeda por el aporte!)**
Java 8 es la versión actual de soporte a largo plazo y aún es ampliamente compatible, aunque el mantenimiento público finaliza en enero de 2019. Para instalar OpenJDK 8, ejecute el siguiente comando:

```shell
sudo apt install openjdk-8-jdk
```

Verifique que esto está instalado con
```shell
java -version
```

Verás una salida como esta:

```
openjdk version "1.8.0_162"
OpenJDK Runtime Environment (build 1.8.0_162-8u162-b12-1-b12)
OpenJDK 64-Bit Server VM (build 25.162-b12, mixed mode)
```
---
**Importante:** para los usuarios de ubuntu están disponibles los paquetes de Debian .DEB (incluso lo puedes instalar vía APT), sin embargo **no te recomiendo seguir este método** a menos que tengas experiencia, ya que es mas facil manejar todo directamente desde la carpeta en la que descomprimiremos todo!

***
## Instalación del Stack ELK
Crear una carpeta para almacenar todas las descargas y descargar lo necesario.
```bash
mkdir ~/elk/
cd ~/elk/

wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.2.tar.gz
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.4.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/logstash/logstash-6.4.2.tar.gz
```

Una vez descargados los archivos, descomrpimir en sus respectivas carpetas.
```bash
tar -xvzf elasticsearch-6.4.2.tar.gz
tar -xvzf kibana-6.4.2-linux-x86_64.tar.gz
tar -xvzf logstash-6.4.2.tar.gz
```

Esto creará tres carpetas distintas, con los respectivos archivos descomprimidos. Primero, vamos a configurar Elasticsearch!

**Asignando más memoria a ElasticSearch**
Nuestra primera meta es entregarle más utilización de RAM a ElasticSearch, ya que no es una guía detallada en el uso completo del a tecnología iremos directo al grano!

_Configurar el archivo **jvm.options:**_
```bash
cd elasticsearch-6.4.2/config
vim jvm.options
```

En este archivo, buscar la línea donde está especificado la cantidad de Ram a utilizar (mínima y máxima) y  subirla a 4gb (o más si puedes).

```diff
-Xms1g   # cambiar por -Xms4g
-Xmx1g   # cambiar por -Xmx4g
```
Con esto deberia estár listo una instalación básica de Elasticsearch!

## Corriendo las herramientas
---
Con lo anterior, todo deberia estar en orden! Revisemos y ejecutemos las herramientas. Para hacerlo, abre 3 terminales distintos (o usa Tmux si sabes usarlo!).

### Ejecutando Elasticsearch
Ubicate en la carpeta de Elasticsearch y ejecuta el software.

```bash
cd ~/elk/elasticsearch-6.4.2/bin
./elasticsearch
```

El output debería cargar un montón de librerias, sin embargo al final debería decir algo parecido a esto:

```output
[2018-10-13T20:18:07,624][INFO ][o.e.t.TransportService   ] [-utqMZo] publish_address {127.0.0.1:9300}, bound_addresses {[::1]:9300}, {127.0.0.1:9300}
```

Sin embargo, podemos comprobar que Elasticsearch está corriendo correctamente en el browser accediendo a la siguente dirección: [http://127.0.0.1:9200](http://127.0.0.1:9200) y nos debería entregar los datos correspondientes a la versión instalada:

```json
{
  "name" : "-utqMZo",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "iQ60l9wtTmiJSy0i6RbfLw",
  "version" : {
    "number" : "6.4.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "04711c2",
    "build_date" : "2018-09-26T13:34:09.098244Z",
    "build_snapshot" : false,
    "lucene_version" : "7.4.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

Listo! **No cierres este terminal.**

### Ejecutando Kibana

Ubícate en la carpeta de Kibana y ejecuta el software.

```bash
cd ~/elk/kibana-6.4.2-linux-x86_64/bin
./kibana
```

El output debería decirte algo como:

```
log   [23:18:14.443] [info][listening][server][http] Server running at http://localhost:5601
```

Sin embargo, si resulta todo bien, deberiamos tener el Kibana corriendo en el puerto 5601! Entra al siguente enlace para comprobar: [http://127.0.0.1:5601](http://127.0.0.1:5601) y debería mostrar la interfaz de Kibana! Asegurate que no haya ningún error!

Tampoco cierres el terminal!

### Ejecutando Logstash

```bash
cd ~/elk/logstash-6.4.2/bin
./logstash
```

Ya que no le hemos dado ningún archivo de configuración al logstash (No te preocupes! lo haremos en el laboratorio). Enviará un error parecido al siguiente

```
ERROR: Pipelines YAML file is empty.
```

Con esto deberias tener todo listo y corriendo para el taller!

## Dudas?
---
Recuerda que si tienes dudas o algo no te resulta estaré dando apoyo a los asistentes durante la semana previa al laboratorio! Me ubican en Telegram o Twitter.

@mdiazcl

Saludos!
Espero que lo disfruten,

_ Miguel Díaz _

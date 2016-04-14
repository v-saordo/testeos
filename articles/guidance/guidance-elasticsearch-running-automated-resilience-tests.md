<properties
   pageTitle="Ejecución de pruebas automatizadas de resistencia de Elasticsearch | Microsoft Azure"
   description="Descripción de cómo ejecutar las pruebas de resistencia en su propio entorno."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Ejecución de pruebas automatizadas de resistencia de Elasticsearch

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Las [pruebas de resistencia y recuperación] hacen referencia a una serie de pruebas que se realizaron en un clúster de Elasticsearch de ejemplo para determinar lo bien que el sistema respondía a algunas formas habituales de error y se recuperaba. Se probaron cuatro escenarios:

- **Error de nodo y reinicio sin pérdida de datos**. Se detiene un nodo de datos y se reinicia tras 5 minutos. Se había configurado Elasticsearch para que no se reasignaran las particiones que faltaban en este intervalo, por lo que no se incurrió en ninguna E/S adicional por mover particiones. Cuando se reinicia el nodo, el proceso de recuperación vuelve a actualizar las particiones de ese nodo.

- **Error de nodo con pérdida de datos catastrófica**. Se detiene un nodo de datos y se borran los datos que contiene para simular un error de disco grave. Después, se reinicia el nodo (después de 5 minutos), que actúa de forma eficaz como reemplazo del nodo original. El proceso de recuperación requiere que se vuelvan a generar los datos que faltan para este nodo y puede suponer la reubicación de las particiones que se mantienen en otros nodos.

- **Error de nodo y reinicio sin pérdida de datos, pero con reasignación de particiones**. Se detiene un nodo de datos y se reasignan las particiones que contiene a otros nodos. Después, se reinicia el nodo y se hacen más reasignaciones para volver a equilibrar el clúster.

- **Actualizaciones graduales**. Se detiene cada nodo del clúster y se reinicia tras un breve intervalo para simular el reinicio de las máquinas después de una actualización de software. Solo se detiene un nodo en cualquier momento dado. Las particiones no se reasignan mientras un nodo esté inactivo.

Estas pruebas se diseñaron con un script para permitir que se ejecutaran de forma automática. En este documento, se describe cómo repetir las pruebas en su propio entorno.

## Requisitos previos

Las pruebas automatizadas requieren los siguientes elementos:

- Un clúster de Elasticsearch.

- Una configuración del entorno JMeter, como se describe en [Guía de pruebas de rendimiento].

- Los siguientes elementos instalados solamente en la máquina virtual maestra de JMeter.

    - Java Runtime 7.

    - Nodejs 4.x.x o posterior.

    - Herramientas de línea de comandos de Git.

## Funcionamiento de los scripts

Los scripts de prueba se deben ejecutar en la máquina virtual maestra de JMeter. Cuando selecciona ejecutar una prueba, los scripts realizan la siguiente secuencia de operaciones:

1.  Se inicia un plan de pruebas de JMeter y se pasan los parámetros que ha especificado.

2.  Se copia un script que realiza las operaciones necesarias para la prueba en una máquina virtual especificada del clúster. Puede tratarse de cualquier máquina virtual con una dirección IP pública o la máquina virtual de *Jumpbox* si ha creado el clúster con la [plantilla de inicio rápido de Elasticsearch para Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch).

3.  Se ejecuta el script en la máquina virtual (o Jumpbox).

En la siguiente imagen, se muestra la estructura del entorno de prueba y el clúster de Elasticsearch. Tenga en cuenta que los scripts de prueba usan SSH para conectarse a cada nodo del clúster para realizar diversas operaciones de Elasticsearch, como detener o reiniciar un nodo.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## Configuración de las pruebas de JMeter

Antes de ejecutar las pruebas de resistencia, debe compilar e implementar las pruebas de JUnit ubicadas en la carpeta *resiliency/jmeter/tests*. Se hace referencia a estas pruebas en el plan de pruebas de JMeter. Para más información, consulte el procedimiento Importing an Existing JUnit Test Project into Eclipse (Importación de un proyecto de prueba de JUnit existente en Eclipse) en [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance][] (Implementación de una muestra de JUnit de JMeter para probar el rendimiento de Elasticsearch).

Hay dos versiones de las pruebas de JUnit en las siguientes carpetas:

- **Elasticsearch17.** El proyecto de esta carpeta genera el archivo Elasticsearch17.jar. Use este archivo JAR para probar las versiones 1.7.x de Elasticsearch.

- **Elasticsearch20**. El proyecto de esta carpeta genera el archivo Elasticsearch20.jar. Use este archivo JAR para probar las versiones 2.0.0 y posteriores de Elasticsearch.

Copie el archivo JAR correspondiente junto con el resto de las dependencias en las máquinas de JMeter. El proceso se describe en el procedimiento sobre la implementación de una muestra de JUnit de JMeter en [Consideraciones para JMeter].

## Configuración de la seguridad de la máquina virtual para cada nodo

Los scripts de prueba requieren que se instale un certificado de autenticación en cada nodo de Elasticsearch en el clúster. Esto permite que los scripts se ejecuten automáticamente sin solicitar ningún nombre de usuario ni contraseña cuando se conectan a las diversas máquinas virtuales.

En primer lugar, inicie sesión en uno de los nodos en el clúster de Elasticsearch (o la máquina virtual de Jumpbox) y después ejecute el siguiente comando para generar una clave de autenticación:

```Shell
ssh-keygen -t rsa
```

Mientras está conectado al nodo de Elasticsearch (o a Jumpbox), ejecute los comandos siguientes para cada nodo del clúster de Elasticsearch. Reemplace `<username>` por el nombre de un usuario válido en cada máquina virtual y reemplace `<nodename>` por el nombre DNS de la dirección IP de la máquina virtual que hospeda el nodo de Elasticsearch. Tenga en cuenta que se le pedirá la contraseña del usuario al ejecutar estos comandos. Para más información, consulte [SSH login without password](http://www.linuxproblem.org/art_9.html) (Inicio de sesión SSH sin contraseña):

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## Descarga y configuración de los scripts de prueba

Los scripts de prueba se proporcionan en un repositorio de Git. Use el procedimiento siguiente para descargar y configurar los scripts.

En la máquina maestra de JMeter donde se ejecutarán las pruebas, abra una ventana del escritorio Git (Git BASH) y clone el repositorio que contiene los scripts, como sigue:

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Vaya a la carpeta *resiliency-tests* y ejecute el siguiente comando para instalar las dependencias necesarias para ejecutar las pruebas:

```Shell
npm install
```

Si el maestro de JMeter se ejecuta en Windows, [descargue PLINK](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) y cópielo en la carpeta *resiliency-tests/lib*.

Si el maestro de JMeter se ejecuta en Linux, no necesita descargar PLINK, pero debe configurar el inicio de sesión SSH sin contraseña entre el maestro de JMeter y el nodo de Elasticsearch o Jumpbox que usó siguiendo los pasos descritos en el procedimiento [Configuración de la seguridad de la máquina virtual para cada nodo](#configuring-vm-security-for-each-node).

Edite el archivo `config.js` y también los siguientes parámetros de configuración para que coincidan con el entorno de prueba y el clúster de Elasticsearch. Estos parámetros son comunes a todas las pruebas:

| Nombre | Descripción | Valor predeterminado |
| ---- | ----------- | ------------- |
| `jmeterPath` | Ruta de acceso local donde se encuentra jmeter | `C:/apache-jmeter-2.13` |
| `resultsPath` | Directorio relativo donde el script vuelca el resultado | `results` |
| `verbose` | Indica si la salida del script está en modo detallado o no. | `true` |
| `remote` | Indica si las pruebas jmeter se ejecutan localmente o en los servidores remotos. | `true` |
| `cluster.clusterName` | Nombre del clúster de Elasticsearch. | `elasticsearch` |
| `cluster.jumpboxIp` | Dirección IP de la máquina de JumpBox |-| 
| `cluster.username` | Usuario administrador que creó durante la implementación del clúster |-| 
| `cluster.password` | Contraseña del usuario administrador |-| 
| `cluster.loadBalancer.ip` | Dirección IP del equilibrador de carga de Elasticsearch |-| 
| `cluster.loadBalancer.url` | Dirección URL base del equilibrador de carga |-|

## Ejecución de las pruebas

Vaya a la carpeta *resiliency-tests* y ejecute el comando siguiente:

```Shell
node app.js
```

Debería aparecer el siguiente menú:

![](./media/guidance-elasticsearch/resilience-testing2.png)

Escriba el número del escenario que desea ejecutar: `11`, `12`, `13` o `21`.

Una vez que seleccione un escenario, la prueba se ejecutará automáticamente. Los resultados se almacenan como conjunto de archivos CSV en una carpeta creada en el directorio *results*. Cada vez que se ejecuta este comando, se crea una nueva carpeta results. Puede usar Excel para analizar y representar gráficamente estos datos.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Guía de pruebas de rendimiento]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Consideraciones para JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[pruebas de resistencia y recuperación]: guidance-elasticsearch-resilience-testing.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->

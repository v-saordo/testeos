<properties
	pageTitle="Instalación de la interfaz de la línea de comandos de Azure | Microsoft Azure"
	description="Instalación de la interfaz de la línea de comandos de Azure para Mac, Linux y Windows para comenzar a utilizar los servicios de Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="danlep"/>

# Instalación de la CLI de Azure

Instale rápidamente la interfaz de la línea de comandos de Azure (CLI de Azure) para utilizar un conjunto de comandos de código abierto basados en shell para crear y administrar recursos en Microsoft Azure. Utilice uno de los paquetes de instalación proporcionados para instalar la CLI de Azure en el sistema operativo, instale la CLI mediante el uso de Node.js y **npm**, o bien instálela como contenedor en un host de Docker. Si desea obtener más opciones y antecedentes, consulte el repositorio del proyecto en [GitHub](https://github.com/azure/azure-xplat-cli).


Una vez que instale la CLI de Azure, podrá [conectarla con su suscripción a Azure](xplat-cli-connect.md) y ejecutar los comandos **azure** desde la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.) para trabajar con los recursos de Azure.




## Uso de un instalador

Están disponibles los siguientes paquetes del instalador:

* [Windows installer][windows-installer]

* [Instalador de OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Instalador de Linux][linux-installer]


## Instalación y uso de Node.js y npm

De manera alternativa, si tiene instalado Node.js en su sistema, utilice el comando siguiente para instalar la CLI de Azure:

	npm install azure-cli -g

> [AZURE.NOTE] En distribuciones Linux, puede que tenga que usar `sudo` para ejecutar correctamente el comando __npm__.

### Instalación de Node.js y npm en distribuciones Linux que usan administración de paquetes [dpkg](http://en.wikipedia.org/wiki/Dpkg)

Las distribuciones más habituales usan [herramienta de empaquetado avanzado (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) u otras herramientas en función del formato del paquete `.deb`. Algunos ejemplos son Ubuntu y Debian.

La mayoría de las distribuciones más recientes requieren la instalación de **nodejs-legacy** para obtener una herramienta **npm** correctamente configurada para instalar la CLI de Azure. En el código siguiente se muestran los comandos que instala **npm** correctamente en Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Algunas de las distribuciones anteriores, como Ubuntu 12.04, requieren la instalación de la distribución actual binaria de Node.js. En el código siguiente se muestra cómo hacer esto mediante la instalación y el uso de **curl**.

>[AZURE.NOTE] Los comandos se toman de las instrucciones de instalación que encontrará [aquí](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Sin embargo, al usar **sudo** como un destino de canalización siempre debe comprobar las secuencias de comandos que va a instalar y validar que hacen exactamente lo que se espera antes de ejecutarlas a través de **sudo**. Un gran poder conlleva una gran responsabilidad.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Instalación de Node.js y npm en distribuciones Linux que usan administración de paquetes [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

La instalación de Node.js en distribuciones basadas en RPM requiere la habilitación del repositorio EPEL. En el código siguiente se muestran las prácticas recomendadas para la instalación en CentOS 7. (Tenga en cuenta que en la primera línea, a continuación, el '-' (guión) es importante)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Instalación de Node.js y npm en Windows y Mac OS X

Puede instalar Node.js y npm en Windows y OS X mediante los instaladores de [Nodejs.org](https://nodejs.org/en/download/). Deberá reiniciar el equipo para completar la instalación. Compruebe si node y npm se instalaron correctamente; para ello, abra una ventana de comandos o terminal y escriba

	npm -v

Si se muestra la versión de npm instalada, puede continuar e instalar la CLI de Azure con

	npm install -g azure-cli

Al final de la instalación, debería ver algo parecido a esto:

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE] Para los sistemas Linux, también puede instalar la CLI de Azure creándola desde el [origen](http://go.microsoft.com/fwlink/?linkid=253472). Para obtener más información acerca de la creación desde el origen, consulte el archivo INSTALL que se incluye en el archivo.

## Uso de un contenedor Docker

En un host de Docker, ejecute:

```
docker run -it microsoft/azure-cli
```

## Ejecución de los comandos de la CLI de Azure
Una vez instalada la CLI de Azure, podrá usar el comando **azure** desde su interfaz de usuario de la línea de comandos (Bash, Terminal, símbolo del sistema, etc.). Por ejemplo, si desea ejecutar el comando help, escriba lo siguiente:

```
azure help
```

Para saber qué versión de la CLI de Azure instaló, escriba lo siguiente:

```
azure --version
```

De este modo, ya está listo. Para obtener acceso a todos los comandos de la CLI a fin de trabajar con sus propios recursos, [conéctese a su suscripción a Azure desde la CLI de Azure](xplat-cli-connect.md).

## Actualización de la CLI

Microsoft publica con frecuencia versiones actualizadas de la CLI de Azure. Reinstale la CLI con el instalador correspondiente a su sistema operativo, o bien, si Node.js y npm están instalados, escriba lo siguiente para actualizarla (en las distribuciones de Linux, es posible que necesite utilizar **sudo**).

```
npm update -g azure-cli
```

## Recursos adicionales

* [Uso de la CLI de Azure con Administrador de recursos de Azure][cliarm]

* [Uso de la CLI de Azure con administración de servicios de Azure][cliasm]

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene preguntas sobre cómo utilizar la CLI de Azure o Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0218_2016-->
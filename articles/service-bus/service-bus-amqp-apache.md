<properties 
   pageTitle="Cómo instalar Apache Qpid Proton-C en una máquina virtual de Linux | Microsoft Azure"
   description="Cómo crear una máquina virtual de CentOS Linux mediante máquinas virtuales de Azure y cómo generar e instalar la biblioteca Apache Qpid Proton-C."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Instalación de Apache Qpid Proton-C en una máquina virtual Linux de Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

En esta sección se muestra cómo crear una máquina virtual de CentOS Linux mediante máquinas virtuales de Azure y cómo descargar, crear e instalar la biblioteca Apache Qpid Proton-C junto con los enlaces de lenguaje Python y PHP. Después de completar estos pasos, podrá ejecutar los ejemplos de Python y PHP incluidos en esta guía.

El primer paso se realiza mediante el [Portal de Azure clásico][]. La captura de pantalla siguiente muestra cómo se crea una máquina virtual de CentOS denominada "scott-centos":

![Proton en una máquina virtual Linux de Azure][0]

Después del aprovisionamiento, el portal muestra lo siguiente:

![Proton en una máquina virtual Linux de Azure][1]

Para iniciar sesión en el equipo, debe conocer el puerto del extremo para SSH. Para obtener este valor desde el [Portal de Azure clásico][], seleccione la máquina virtual recién creada y haga clic en la pestaña **Puntos de conexión**. La captura de pantalla siguiente muestra que el puerto SSH público para este equipo es 57146.

![Proton en una máquina virtual Linux de Azure][2]

Ahora puede conectarse con el equipo mediante SSH. En este ejemplo se usa la herramienta PuTTY, tal como se muestra en la siguiente captura de pantalla:

![Proton en una máquina virtual Linux de Azure][3]

Para las aplicaciones PHP y Python, este ejemplo usa las bibliotecas de cliente de Proton de Apache. Estas bibliotecas están disponibles para su descarga en [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). El archivo Léame del paquete de distribución explica los pasos necesarios para instalar las dependencias y compilar Proton. Este es un resumen de los pasos:

1.  Edite el archivo de configuración yum (/etc/yum.conf) y marque como comentario la exclusión de las actualizaciones para encabezados de kernel (# exclude=kernel*). Esto es necesario para instalar el compilador de gcc.

2.  Instale los paquetes de requisitos previos:

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Descargue la biblioteca Proton:

	```
	[azureuser@this-user ~]$ wget http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		--2013-05-23 21:27:55-- http://www.bizdirusa.com/mirrors/apache/qpid/proton/0.4/qpid-proton-0.4.tar.gz 
		Resolving www.bizdirusa.com... 205.186.175.195 
		Connecting to www.bizdirusa.com|205.186.175.195|:80... connected. 
		HTTP request sent, awaiting response... 200 OK 
		Length: 456693 (446K) [application/x-gzip] 
		Saving to: âqpid-proton-0.4.tar.gzâ

		100%[======================================>] 456,693 --.-K/s in 0.06s

		2015-05-23 21:27:55 (6.84 MB/s) - qpid-proton-0.4.tar.gz
	```

1.  Extraiga el código Proton del archivo de distribución:

	```
	tar xvfz qpid-proton-0.4.tar.gz
	```

1.  Cree e instale el código mediante los pasos siguientes, procedentes del archivo Léame:

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

Después de realizar estos pasos, Proton estará instalado en el equipo y listo para su uso.

## Pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

- [Información general sobre AMQP para el Bus de servicio][]

[Información general sobre AMQP para el Bus de servicio]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0128_2016-->
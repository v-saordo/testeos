<properties 
	pageTitle="Certificados de administración y servicios en la nube | Microsoft Azure" 
	description="Aprenda a crear y a usar certificados con Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>

# Introducción a los certificados para los servicios en la nube de Azure
Los certificados se usan en Azure para los servicios en la nube ([certificados de servicio](#what-are-service-certificates)) y para realizar la autenticación con la API de administración ([certificados de administración](#what-are-management-certificates) cuando se usa el Portal de Azure clásico, no ARM). En este tema se proporciona información general de ambos tipos de certificado, cómo [crearlos](#create) y cómo [implementarlos](#deploy) en Azure.

Los certificados usados en Azure son certificados x.509 v3 y pueden estar firmados por otro certificado de confianza o estar autofirmados. Un certificado autofirmado está firmado por su propio creador y, por ello, no es de confianza de forma predeterminada. La mayoría de los exploradores pueden pasar por alto esto. Solo usted debe usar los certificados autofirmados cuando desarrolle y pruebe sus servicios en la nube.

Los certificados utilizados por Azure pueden contener una clave privada o pública. Los certificados tienen una huella digital que proporciona un medio para identificarlos de forma inequívoca. Esta huella digital se utiliza en el [archivo de configuración](cloud-services-configure-ssl-certificate.md) de Azure para identificar qué certificado debe usar un servicio en la nube.

## ¿Qué son los certificados de servicio?
Los certificados de servicio están vinculados a los servicios en la nube y posibilitan la comunicación segura hacia y desde el servicio. Por ejemplo, si implementó un rol web, desearía proporcionar un certificado que pueda autenticar un punto de conexión HTTPS expuesto. Los certificados de servicio, definidos en la definición de servicio, se implementan automáticamente en la máquina virtual que está ejecutando una instancia del rol.

Puede cargar certificados de servicio en el Portal de Azure clásico mediante el Portal de Azure clásico o mediante la API de administración de servicios. Los certificados de servicio se asocian a un servicio en la nube específico y se asignan a una implementación en el archivo de definición de servicio.

Los certificados de servicio se pueden administrar independientemente de los servicios y pueden ser administrados por distintas personas. Por ejemplo, un desarrollador puede cargar un paquete de servicio que hace referencia a un certificado que un administrador de TI ha cargado previamente en Azure. Un administrador de TI puede administrar y renovar el certificado cambiando la configuración del servicio sin necesidad de cargar un nuevo paquete de servicio. Esto es posible porque el nombre lógico del certificado y su nombre y ubicación del almacén se especifican en el archivo de definición de servicio, mientras que la huella digital del certificado se especifica en el archivo de configuración de servicio. Para actualizar el certificado, solo es necesario cargar un nuevo certificado y cambiar el valor de huella digital en el archivo de configuración de servicio.

## ¿Qué son los certificados de administración?
Los certificados de administración permiten realizar autenticación con la API de administración de servicios proporcionada por Azure clásico. Muchos programas y herramientas (como Visual Studio o el SDK de Azure) utilizarán estos certificados para automatizar la configuración y la implementación de varios servicios de Azure. Estos no están relacionados realmente con servicios en la nube.

>[AZURE.WARNING] Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados.

### Limitaciones
Hay un límite de 100 certificados de administración por suscripción. También hay un límite de 100 certificados de administración para todas las suscripciones bajo un identificador de usuario de un administrador de servicio específico. Si ya ha se ha usado el identificador de usuario para el administrador de la cuenta para agregar 100 certificados de administración y se necesitan más certificados, puede agregar un coadministrador para añadir certificados adicionales.

Antes de agregar más de 100 certificados, vea si puede reutilizar un certificado existente. El uso de coadministradores agrega complejidad potencialmente innecesaria al proceso de administración de certificados.


<a name="create"></a>
## Creación de un nuevo certificado autofirmado
Puede utilizar cualquier herramienta disponible para crear un certificado autofirmado, siempre que cumpla esta configuración:

* Un certificado X.509.
* Contiene una clave privada.
* Creado para intercambio de claves (archivo .pfx).
* El nombre de sujeto debe coincidir con el dominio usado para tener acceso al servicio en la nube.
    > No se puede adquirir un certificado SSL para el dominio cloudapp.net (ni para ningún dominio relacionado con Azure); el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, **contoso.net**, no **contoso.cloudapp.net**.
* Mínimo de cifrado de 2.048 bits.
* **Solo certificados de servicio**: el certificado de cliente debe encontrarse en el almacén de certificados *Personal*.

Hay dos métodos sencillos para crear un certificado en Windows, con la utilidad `makecert.exe` o con IIS.

### Makecert.exe

Esta utilidad se instala con Visual Studio 2013/2015. Es una utilidad de consola que le permite crear e instalar certificados. Si inicia el acceso directo de **Símbolo del sistema para desarrolladores de VS2015** que se crea al instalar Visual Studio, aparecerá un símbolo del sistema con esta herramienta en la ruta de acceso.

    makecert -sky exchange -r -n "CN=[CertificateName]" -pe -a sha1 -len 2048 -ss My -sv [CertificateName].pvk [CertificateName].cer


### Internet Information Services (IIS)

Hay muchas páginas en Internet que explican cómo hacer esto con IIS. [Aquí](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) hay una excelente que encontré en la que se explica bien.

### Java
Puede usar Java para [crear un certificado](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### Linux
En [este](..\virtual-machines\virtual-machines-linux-use-ssh-key.md) artículo se describe cómo crear certificados con SSH.

## Pasos siguientes

[Cargue el certificado de servicio en el Portal de Azure clásico](cloud-services-configure-ssl-certificate.md) (o en el [Portal de Azure](cloud-services-configure-ssl-certificate-portal.md)).

Cargue un [certificado de API de administración](../azure-api-management-certs.md) en el Portal de Azure clásico.

>[AZURE.NOTE] El Portal de Azure no usa certificados de administración para tener acceso a la API, sino cuentas de usuario.

<!---HONumber=AcomDC_0128_2016-->
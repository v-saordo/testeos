<properties
   pageTitle="Publish-WebApplicationWebSite (script de Windows PowerShell) | Microsoft Azure"
   description="Aprenda a publicar un proyecto web en un sitio web de Azure. Este script crea los recursos necesarios en su suscripción de Azure si no existen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Publicación de WebApplicationWebSite (script de Windows PowerShell)

##Sintaxis

Publica un proyecto web en un sitio web de Azure. El script crea los recursos necesarios en su suscripción de Azure si no existen.

	Publish-WebApplicationWebSite
	–Configuration <configuration>
	-SubscriptionName <subscriptionName>
	-WebDeployPackage <packageName>
	-DatabaseServerPassword @{Name = "name"; Password = "password"}
	-SendHostMessagesToOutput
	-Verbose


## Configuración

La ruta de acceso al archivo de configuración JSON que describe los detalles de la implementación.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguna|
|¿Necesario?|true|
|Posición|con nombre|
|Valor predeterminado|Ninguna|
|¿Aceptar la entrada de la canalización?|false|
|¿Aceptar caracteres comodín?|false|

## SubscriptionName

El nombre de la suscripción de Azure en la que desea crear el sitio web.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguna|
|¿Necesario?|false|
|Posición|con nombre|
|Valor predeterminado|Ninguna|
|¿Aceptar la entrada de la canalización?|false|
|¿Aceptar caracteres comodín?|false|

## WebDeployPackage

La ruta de acceso al paquete de implementación web para publicar en el sitio web. Puede crear este paquete mediante el Asistente de publicación web en Visual Studio. Para obtener más información, consulte [Introducción a Servicios en la nube de Azure y ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguna|
|¿Necesario?|false|
|Posición|con nombre|
|Valor predeterminado|Ninguna|
|¿Aceptar la entrada de la canalización?|false|
|¿Aceptar caracteres comodín?|false|

## DatabaseServerPassword

El nombre de usuario y la contraseña de la base de datos SQL en Azure.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguna|
|¿Necesario?|false|
|Posición|con nombre|
|Valor predeterminado|Ninguna|
|¿Aceptar la entrada de la canalización?|false|
|¿Aceptar caracteres comodín?|false|

## SendHostMessagesToOutput

Si es true, imprimir mensajes del script a la secuencia de salida.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguna|
|¿Necesario?|false|
|Posición|con nombre|
|Valor predeterminado|false|
|¿Aceptar la entrada de la canalización?|false|
|¿Aceptar caracteres comodín?|false|

## Comentarios

Para obtener una explicación completa de cómo usar el script para crear entornos de desarrollo y pruebas, consulte [Utilizar scripts de Windows PowerShell para la publicación en entornos de desarrollo y pruebas](vs-azure-tools-publishing-using-powershell-scripts.md).

El archivo de configuración JSON especifica los detalles de lo que va a implementarse. Incluye la información que especificó cuando creó el proyecto, como el nombre y el nombre de usuario para el sitio web. También incluye la base de datos que se va a aprovisionar, si la hubiera. El código siguiente muestra un archivo de configuración de JSON de ejemplo:

	{
	    "environmentSettings": {
	        "webSite": {
	            "name": "WebApplication10554",
	            "location": "West US"
	        },
	        "databases": [
	            {
	                "connectionStringName": "DefaultConnection",
	                "databaseName": "WebApplication10554_db",
	                "serverName": "iss00brc88",
	                "user": "sqluser2",
	                "password": "",
	                "edition": "",
	                "size": "",
	                "collation": "",
	                "location": "West US"
	            }
	        ]
	    }
	}

Puede editar el archivo de configuración de JSON para cambiar lo que se implementa. Una sección webSite es obligatoria pero la sección database es opcional.

## Pasos siguientes

Para obtener más información, consulte [Publish-WebApplicationVM (script de Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

<!---HONumber=AcomDC_1223_2015-->
<properties 
 pageTitle="Autenticación saliente de Programador" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Autenticación saliente de Programador

Puede que los trabajos de Programador tengan que llamar a servicios que requieren autenticación. De este modo, un servicio llamado puede determinar si el trabajo de Programador puede tener acceso a sus recursos. Algunos de estos servicios incluyen otros servicios de Azure, Salesforce.com, Facebook y sitios web personalizados que sean seguros.

## Incorporación y eliminación de autenticación

La incorporación de autenticación a un trabajo de Programador es sencilla: agregue un elemento secundario JSON `authentication` al elemento `request` al crear o actualizar un trabajo. Los secretos que se pasan al servicio Programador en una solicitud PUT, PATCH o POST, como parte del objeto `authentication`, nunca se devuelven en respuestas. En las respuestas, la información secreta se establece en null o puede que tenga un token público que represente la entidad autenticada.

Para quitar la autenticación, incluya explícitamente una solicitud PUT o PATCH en el trabajo, lo que establece el objeto `authentication` en null. No verá ninguna propiedad de autenticación en la respuesta.

Actualmente, los únicos tipos de autenticación compatibles son el modelo `ClientCertificate` (para usar los certificados de cliente SSL/TLS), el modelo `Basic` (para la autenticación básica) y el modelo `ActiveDirectoryOAuth` (para autenticación ActiveDirectoryOAuth).

## Cuerpo de la solicitud en autenticación ClientCertificate

Al agregar autenticación mediante el modelo `ClientCertificate`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento|Descripción|
|:---|:---|
|_authentication (parent element)_|Objeto de autenticación para usar un certificado de cliente SSL.|
|_type_|Obligatorio. Tipo de autenticación. En certificados de cliente SSL, el valor debe ser `ClientCertificate`.|
|_pfx_|Obligatorio. Contenido codificado en base 64 del archivo PFX.|
|_password_|Obligatorio. Contraseña de acceso al archivo PFX.|


## Cuerpo de la respuesta en autenticación ClientCertificate

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar un certificado de cliente SSL.|
|_type_ |Tipo de autenticación. Para los certificados de cliente SSL, el valor es `ClientCertificate`.|
|_certificateThumbprint_ |Huella digital del certificado.|
|_certificateSubjectName_ |Nombre distintivo del sujeto del certificado.|
|_certificateExpiration_ |Fecha de expiración del certificado|

## Ejemplo de solicitud y respuesta en autenticación ClientCertificate

La solicitud de ejemplo siguiente realiza una solicitud PUT que incorpora autenticación `ClientCertificate`. La solicitud es la siguiente:


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una vez enviada esta solicitud, la respuesta es la siguiente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
## Cuerpo de la solicitud en autenticación básica

Al agregar autenticación mediante el modelo `Basic`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento|Descripción|
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación básica.|
|_type_ |Obligatorio. Tipo de autenticación. En autenticación básica, el valor debe ser `Basic`.|
|_username_ |Obligatorio. Nombre de usuario para autenticar.|
|_password_ |Obligatorio. Contraseña para autenticar.|

## Cuerpo de la respuesta en autenticación básica

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento|Descripción|
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación básica.|
|_type_ |Tipo de autenticación. En autenticación básica, el valor es `Basic`.|
|_username_ |Nombre del usuario autenticado.|

## Ejemplo de solicitud y respuesta en autenticación básica

La solicitud de ejemplo siguiente realiza una solicitud PUT que incorpora autenticación `Basic`. La solicitud es la siguiente:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una vez enviada esta solicitud, la respuesta es la siguiente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Cuerpo de la solicitud en autenticación ActiveDirectoryOAuth

Al agregar autenticación mediante el modelo `ActiveDirectoryOAuth`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth.|
|_type_ |Obligatorio. Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor debe ser `ActiveDirectoryOAuth`.|
|_tenant_ |Obligatorio. Identificador del inquilino de Azure AD.|
|_audience_ |Obligatorio. Se establece en https://management.core.windows.net/.|
|_clientId_ |Obligatorio. Proporcione el identificador de cliente para la aplicación de Azure AD.|
|_secret_ |Obligatorio. Secreto del cliente que solicita el token.|

### Determinación del identificador del inquilino

Encontrará el identificador del inquilino de Azure AD ejecutando `Get-AzureAccount` en Azure PowerShell.

## Cuerpo de la respuesta en autenticación ActiveDirectoryOAuth

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth.|
|_type_ |Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`.|
|_tenant_ |Identificador del inquilino de Azure AD. |
|_audience_ |Se establece en https://management.core.windows.net/.|.
|_clientId_ |Identificador de cliente para la aplicación de Azure AD.|

## Ejemplo de solicitud y respuesta en autenticación de ActiveDirectoryOAuth

La solicitud de ejemplo siguiente realiza una solicitud PUT que incorpora autenticación `ActiveDirectoryOAuth`. La solicitud es la siguiente:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una vez enviada esta solicitud, la respuesta es la siguiente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Otras referencias
 

 [¿Qué es Programador?](scheduler-intro.md)
 
 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)


  

 
  

<!---HONumber=AcomDC_0128_2016-->
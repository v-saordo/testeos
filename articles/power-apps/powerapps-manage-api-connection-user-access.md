<properties
	pageTitle="Agregar o crear una nueva API y otorgar permisos a usuarios en PowerApps | Microsoft Azure"
	description="Agregar, crear y configurar una nueva API, una conexión o perfil de conexión y conceder permisos y derechos con el acceso de usuario en el Portal de Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Agregar una API nueva, una conexión y conceder acceso a los usuarios

Las API existen en un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md). Las API se pueden a partir de las API disponibles para PowerApps, desde las aplicaciones de API alojadas en el entorno del Servicio de aplicaciones o desde Swagger 2.0. Hay muchas API preconfiguradas disponibles que pueden agregarse fácilmente a su PowerApps. También puede cargar su propia API en formato JSON o Swagger 2.0.

En este tema:

- se enumeran los pasos para agregar una API a PowerApps y proporcionar a los usuarios de su compañía permisos para usar la API, incluido modificando sus propiedades.
- Enumera los pasos para agregar una conexión a su API y proporcionar a los usuarios de su compañía permisos para usar la conexión.


#### Requisitos previos para empezar

- Habilite [PowerApps en su suscripción de Azure](powerapps-get-started-azure-portal.md).
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md).
- Cree una API siguiendo cualquiera de los métodos siguientes:  
	- Cree una [API administrada por Microsoft o una API administrada por TI](powerapps-register-from-available-apis.md).
	- Cree una API hospedada dentro del [entorno del Servicio de aplicaciones](powerapps-register-api-hosted-in-app-service.md).
	- Créela mediante una [definición de API de Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).


## Dar a los usuarios acceso a la API
Ahora que la API se ha creado y agregado a su entorno del Servicio de aplicaciones, ha llegado el momento de dar a los usuarios de su compañía los permisos para usarla.

1. En PowerApps, seleccione **Administrar API** y seleccione su API. Por ejemplo, si creó una API de *MS Power BI*, selecciónela para abrir su hoja. Seleccione **Acceso del usuario a la API**: ![][1]  

2. Seleccione **Agregar** para agregar usuarios y seleccione los derechos. Cuando haya terminado, seleccione **Agregar** para guardar los cambios. El recuento de usuarios o grupos aumenta en la ventana **Acceso del usuario a la API**.


## Agregar una nueva conexión a la API
El siguiente paso consiste en crear la "conexión" a la API, que es como una cadena de conexión. Esto permite a la API conectarse correctamente a su sistema de "back-end". Para la vista previa pública de PowerApps Enterprise, solamente se puede agregar y configurar la conexión de SQL Server. Se están agregando más.

- [Crear la conexión de SQL Server](powerapps-create-api-sqlserver.md)

## Proporcionar a los usuarios acceso en tiempo de ejecución a la conexión
Ahora conceda a los usuarios de dentro de su compañía permisos para usar la conexión.

1. Abra la API, seleccione **Conexiones** y, a continuación, seleccione la conexión específica. De este modo se abrirá una nueva hoja que indicará el nombre de su conexión en la parte superior. 
2. En esta nueva hoja, seleccione **Acceso de usuarios a la conexión**. En el ejemplo siguiente, se selecciona la conexión de **túnel híbrido**. Se abre la hoja nueva y aquí es donde se selecciona **Acceso de usuarios a la conexión**: ![][2]
  
3. En **Acceso de usuarios a la conexión**, seleccione **Agregar** y, a continuación, seleccione el permiso que desee asignar: ![][3]
  
4. Agregue su usuario o grupo. Seleccione **Agregar** para guardar sus cambios.

Ahora que los usuarios tienen permisos para la API y su conexión, estos pueden agregar estas API a sus aplicaciones creadas en PowerApps. Concretamente:

- Los usuarios pueden ver la API que aparece en **Conexiones disponibles** en PowerApps.
- Los usuarios pueden ver la conexión que aparece en **Mis conexiones** en PowerApps.


## Eliminar una API
También puede eliminar una API agregada anteriormente. En PowerApps, seleccione las **API de administración**, seleccione la API y, a continuación, **Eliminar**: ![][4]


## Resumen y pasos siguientes
En este tema:

- Ha agregado una API y concedido a los usuarios de su compañía derechos de uso. También puede llevar a cabo estos pasos para administrar el acceso en tiempo de ejecución en cualquier momento. Por ejemplo, si el usuario A deja la compañía, puede usar el Portal de Azure para quitar fácilmente los permisos de este usuario. Sucede lo mismo si el usuario B se une a su compañía.
- Ha agregado una conexión (que es similar a una cadena de conexión). Este paso permite que la API hospedada en Azure se conecte a su sistema, como un SQL Server local. También ha concedido a los usuarios de dentro de su compañía permisos para usar la conexión. 
- Ha trabajado con hojas diferentes, dependiendo de la tarea. Para agregar una conexión, abra la API y use su hoja. Para conceder acceso de usuarios, abra la API o la conexión, en función de a qué le esté concediendo acceso. 
- También puede eliminar cualquiera de las API creadas en su entorno de servicio de aplicaciones.

A continuación, puede [administrar y supervisar sus PowerApps](powerapps-manage-monitor-usage.md).

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_1203_2015-->
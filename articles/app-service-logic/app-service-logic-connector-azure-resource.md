<properties
   pageTitle="Uso del conector de recursos de Azure en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de recursos de Azure o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="stepsic"/>

# Introducción al conector de recursos de Azure y su incorporación a su aplicación lógica 
Use el conector de recursos de Azure para administrar de manera fácil los recursos de Azure dentro de la aplicación lógica.

## Creación del Conector de recursos de Azure
Para usar la aplicación de API del Conector de recursos de Azure, deberá crear primero una instancia de ella. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del Conector del Administrador de recursos de Azure en Azure Marketplace.

Para configurarlo, tendrá que establecer una entidad de servicio con permisos para realizar lo que desee hacer en Azure. Todas las llamadas se realizan en nombre de esta entidad de servicio que configura. Esto le permitirá definir el ámbito del conector para utilizar solo exactamente lo que desea hacer y nada más.

David Ebbo ha escrito [una gran cantidad de entradas de blog ](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sobre su configuración. Siga todas las instrucciones y obtenga el **Id. de inquilino**, el **Id. de cliente** y el **Secreto**. Estos tres campos, más el de **Id. de suscripción** son los necesarios para configurar el Conector.

## Uso del Conector de recursos de Azure en el diseñador de Aplicaciones lógicas
### Desencadenador
Hay dos desencadenadores admitidos en el Conector:

Nombre | Descripción 
---- | ----------- 
Se produce el evento | Se activa cuando se produce un evento en un recurso en la suscripción. 
Métrica supera el umbral | Se activa cuando una métrica alcanza un umbral determinado.

### Acción

Del mismo modo, puede proporcionar un gran número de acciones dentro de la suscripción de Azure:
 
Para **Grupos de recursos**, puede:

Nombre | Descripción 
---- | -----------
Enumeración de grupos de recursos | Enumere todos los grupos de recursos en la suscripción.
Obtención de un grupo de recursos | Obtenga un grupo de recursos por su identificador.
Creación de un grupo de recursos | Cree o actualice un grupo de recursos.
Eliminación de un grupo de recursos | Elimine un grupo de recursos.

Para **Recursos**, puede:

Nombre | Descripción 
---- | -----------
Enumeración de recursos | Enumere recursos en la suscripción por diferentes tipos de filtros.
Obtención de recursos | Obtenga un único recurso por su id. de recurso.
Creación o actualización de recursos | Cree un recurso o actualice uno existente. Debe proporcionar todas las propiedades para ese recurso.
Acción de recursos | Realice cualquier otra acción en un recurso. Necesitará saber el nombre de acción y la carga útil que precisa esta acción (si corresponde).
Eliminación de un recurso | Elimine un recurso.

Para **Proveedores de recursos**, puede:

Nombre | Descripción 
---- | -----------
Enumeración de proveedores de recursos | Enumere todos los proveedores de recursos disponibles en esta suscripción.

Para **Implementaciones de grupo de recursos**, puede:

Nombre | Descripción 
---- | -----------
Enumeración de implementaciones | Enumere todas las implementaciones en un grupo de recursos.
Obtención de implementación | Obtenga una implementación de plantilla por su id.
Creación de una implementación | Cree una nueva implementación de grupo de recursos proporcionando una plantilla.

Para **Eventos** acerca de los recursos, puede:

Nombre | Descripción
---- | -----------
Obtención de eventos | Obtenga eventos en una suscripción o para un recurso.

Para **Métricas** acerca de recursos, puede:

Nombre | Descripción
---- | -----------
Obtención de métricas | Obtenga una métrica para un id. de recurso.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_1203_2015-->
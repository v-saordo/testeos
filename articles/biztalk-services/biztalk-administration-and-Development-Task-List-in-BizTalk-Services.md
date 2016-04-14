<properties 
	pageTitle="Lista de tareas de administración y desarrollo de los Servicios de BizTalk | Servicios de BizTalk de Microsoft Azure" 
	description="" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="msftman" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="deonhe"/>

# Lista de tareas de administración y desarrollo de los Servicios de BizTalk  

## Introducción
Cuando se trabaja con Servicios de BizTalk de Microsoft Azure, hay varios componentes locales y basados en la nube que hay que tener en cuenta. Para empezar, piense en el siguiente flujo de proceso:

|Paso|Quién es el responsable|Tarea|Vínculos relacionados|
|----|----|----|----|
1\.|Administrador|Crear la suscripción de Microsoft Azure con una cuenta de Microsoft o una cuenta de la organización|[Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2\.|Administrador|Cree o aprovisione un servicio de BizTalk.|[Crear un Servicio de BizTalk mediante el portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3\.|Administrador|Registrarse a usted o registrar la implementación de los Servicios de BizTalk de su empresa|[Registrar y actualizar una implementación del servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4\.|Administrador|Se aplica si la aplicación usa el servicio de adaptador de BizTalk para conectarse a un sistema de línea de negocio (LOB) local o usa un destino de tema o cola. Cree el espacio de nombres del Bus de servicio de Azure. Dé los valores de este espacio de nombres, del nombre del emisor del Bus de servicio y de la clave del emisor del Bus de servicio al desarrollador.|[Crear o modificar un espacio de nombres del servicio del Bus de servicio](../service-bus/service-bus-dotnet-how-to-use-queues.md) y [Obtener valores del nombre de emisor y de la clave de emisor](biztalk-issuer-name-issuer-key.md)|
|5\.|Developer|Instale el SDK y cree el proyecto del Servicio de BizTalk en Visual Studio.|[Instalar SDK de Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx) y [Crear puntos de conexión de mensajería enriquecida en Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6\.|Developer|Implemente su proyecto del Servicio de BizTalk para su Servicio de BizTalk hospedado en Azure.|[Implementar y actualizar el proyecto de Servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7\.|Administrador|Se aplica si está usando EDI. Puede agregar partner y crear acuerdos en el Portal de servicios de BizTalk de Microsoft Azure. Al crear un contrato, puede agregar el puente o las transformaciones creadas por el desarrollador a la configuración del acuerdo.|[Configuración de EDI, AS2 y EDIFACT en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8\.|Administrador|Con el Portal de Azure clásico, supervise el estado de su Servicio de BizTalk, incluidas las métricas de rendimiento.|[Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9\.|Administrador|Mediante el portal de Servicios de BizTalk de Microsoft Azure, administre los artefactos usados por los Servicios de BizTalk y realice un seguimiento de los mensajes a medida que los archivos puente los procesan.|[Uso del portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10\.|Administrador|Cree un plan de copia de seguridad para realizar una copia de seguridad del Servicio de BizTalk.|[Continuidad empresarial y recuperación ante desastres en Servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## Pasos siguientes
[Tutoriales y ejemplos](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Creación del proyecto en Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalación del SDK de Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## Conceptos
[Crear el proyecto en Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx) [Mensajería EDI, AS2 y EDIFACT (Negocio a negocio)](https://msdn.microsoft.com/library/azure/hh689898.aspx)
## Otros recursos  
[Agregar puntos de conexión de mensajería de origen, destino y puente](https://msdn.microsoft.com/library/azure/hh689877.aspx) [Obtener información y crear transformaciones y mapas de mensajes](https://msdn.microsoft.com/library/azure/hh689905.aspx) [Uso del servicio del adaptador de BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx) [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

<!---HONumber=AcomDC_0302_2016-->
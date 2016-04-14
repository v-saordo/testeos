<properties 
   pageTitle="Creación de un proceso B2B en el Servicio de aplicaciones de Azure | Microsoft Azure" 
   description="Información general para crear un proceso negocio a negocio" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
	ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Creación de un proceso B2B

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

## Escenario empresarial 
Contoso y Northwind son dos socios comerciales. Contoso (el minorista) envía los pedidos de compra a Northwind (proveedor) a través de un transporte industrial como AS2. Northwind almacena todos los pedidos entrantes en su almacenamiento en nube. Los pedidos de compra son mensajes XML entre estos dos socios. Una vez que el mensaje se almacena en el almacenamiento en la nube de Northwind, los procesos internos de Northwind controlan el pedido a partir de ese punto.
 
El objetivo de este tutorial consiste en determinar cómo Northwind puede establecer un proceso empresarial mediante el cual puede recibir mensajes (pedidos de compra en XML) de su socio Contoso a través de AS2 y, a continuación, conservarlo en su almacenamiento en la nube.


## Funcionalidades mostradas 
Este tutorial le ayuda a mostrar las siguientes funcionalidades:

- **Transporte de mensajes**: el minorista y el proveedor pueden estar en distintas plataformas, lo que no les impide comunicarse entre sí. En este tutorial se comunican a través de AS2 (Applicability Statement 2). AS2 es una conocida forma de transportar datos entre socios comerciales en comunicaciones negocio a negocio.
- **Persistencia de datos**: una vez que el mensaje se ha recibido a través de AS2, Northwind desea conservarlo antes de continuar con el procesamiento. Puede usar un conector para conservar mensajes en su almacenamiento en la nube. En este tutorial, se aprovechan los blobs de Azure como el almacenamiento en la nube para Northwind.
- **Creación de un proceso empresarial**: en un flujo, varias aplicaciones de API se pueden unir para lograr un resultado empresarial, como se muestra aquí.


## Antes de empezar
En este tutorial se supone que tiene conocimientos básicos de Servicios de aplicaciones de Azure, sabe cómo crear aplicaciones de API y unir un flujo.


## Pasos para lograr el escenario empresarial
**Creación y configuración de las aplicaciones de API necesarias**

1. Cree una instancia del **conector Blob de almacenamiento de Azure**. Esto requiere las credenciales para una cuenta de almacenamiento de Azure. Asegúrese de que está listo antes de comenzar la creación.
2. Cree una instancia de **Administración de socios comerciales de BizTalk**. Para que esto funcione, se necesita una base de datos SQL en blanco. Asegúrese de que está listo antes de comenzar la creación.
3. Cree una instancia del **conector AS2**. Para que esto funcione, también se necesita una base de datos SQL en blanco. Asegúrese de que está listo antes de comenzar la creación. Además, si desea archivar mensajes como parte del procesamiento de AS2, puede proporcionar credenciales a un blob de Azure durante su creación.
4. Configure el servicio TPM (Administración de socios comerciales) que se crea:  
	1. Vaya a la instancia del servicio TPM creada como parte de los pasos anteriores.
	2. Use la opción **Socios** en *Componentes* para **agregar** un nuevo asociado denominado **Contoso** y, en su perfil, agregue la identidad AS2 necesaria.
	3. Use la opción **Socios** en *Componentes* para **agregar** un nuevo asociado denominado **Northwind** y, en su perfil, agregue la identidad AS2 necesaria.
	4. Use la opción **Acuerdos** en *Componentes* para **agregar** un nuevo acuerdo de AS2 entre Contoso y Northwind. Aquí, Northwind será el socio hospedado y Contoso el socio invitado. Según sea necesario, configure la firma, el cifrado, la compresión y las confirmaciones durante la creación de este acuerdo. En caso de que sea necesario usar certificados, se pueden cargar a través de la opción **Certificados** cuando se examine el servicio TPM que se crea.


## Creación de un flujo o proceso empresarial
1. Cree un nuevo flujo en el que el primer paso sea AS2. Arrastre y coloque el **conector AS2** y elija la instancia ya creada. Elección del desencadenador como la funcionalidad: ![][1]  
2. A continuación, arrastre y coloque el **conector de blobs de almacenamiento de Azure** y elija la instancia ya creada. Elija la acción como la funcionalidad y, dentro de ella, seleccione **Cargar blob** como la funcionalidad deseada. Realice la configuración según corresponda.
3. Ahora, cree o implemente el flujo.


## Procesamiento de mensajes y solución de problemas
1. Es el momento de probar el flujo que hemos implementado. Envíe mensajes XML encapsulados en AS2 (según el acuerdo de AS2 creado anteriormente) al punto de conexión de AS2 obtenido por la instancia del conector AS2 que se creó. Puede que necesite configurar la autenticación para el extremo para que sea accesible públicamente.
2. La información de ejecución sobre el flujo aparece examinando el flujo y, a continuación, entrando en la instancia de flujo que se ejecutó.
3. Para obtener información de procesamiento de AS2, vaya a la instancia del conector AS2 implicada y, a continuación, continúe y entre en la parte de seguimiento. Puede usar los filtros necesarios para restringir la vista a la información que se desea.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 

<!---HONumber=AcomDC_0224_2016-->
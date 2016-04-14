<properties 
	pageTitle="Nombre del emisor y clave del emisor en Servicios de BizTalk | Microsoft Azure" 
	description="Obtenga información acerca de cómo recuperar el Nombre del emisor y la Clave de emisor para el Bus de servicio o Control de acceso (ACS) en Servicios de BizTalk. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="mandia"/>




# Servicios de BizTalk: nombre del emisor y clave del emisor

Los servicios de BizTalk de Azure usan el nombre y la clave de emisor de Bus de servicio, además del nombre y la clave de emisor del servicio de control de acceso. Concretamente:

Tarea | Nombre de emisor y clave de emisor
--- | ---
Implementación de la aplicación desde Visual Studio | Nombre y clave de emisor del servicio de control de acceso
Configuración del Portal de servicios de BizTalk de Azure | Nombre y clave de emisor del servicio de control de acceso
Creación de relés de LOB con los servicios de adaptador de BizTalk en Visual Studio | Nombre de emisor y clave de emisor del bus de servicio

Este tema incluye los pasos necesarios para recuperar el nombre y la clave de emisor.

## Nombre y clave de emisor del servicio de control de acceso
Los siguientes elementos usan el nombre y la clave de emisor del servicio de control de acceso:

- Su aplicación del servicio de BizTalk de Azure creada en Visual Studio: para implementar correctamente la aplicación del servicio de BizTalk de Visual Studio en Azure, debe escribir el nombre y la clave de emisor del servicio de control de acceso. 
- Portal de Servicios de BizTalk de Azure: al crear un servicio de BizTalk Service y abrir el Portal de Servicios de BizTalk, el nombre del emisor de control de acceso y la clave de emisor se registran automáticamente para las implementaciones con los mismos valores de control de acceso.

### Copia y pegado del nombre y la clave de emisor de control de acceso

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3. Seleccione su servicio de BizTalk. 
4. Seleccione **Información de conexión** en la barra de tareas. El espacio de nombres del servicio de control de acceso, el emisor predeterminado (nombre de emisario) y la clave predeterminada (clave de emisor) se incluyen y se pueden copiar y pegar.  

En resumen: Nombre de emisor = Emisor predeterminad Clave de emisor = Clave predeterminada


También puede seleccionar **Abrir Portal de administración de AC**S para obtener los valores de control de acceso:

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3. Seleccione su servicio de BizTalk.
4. Seleccione el botón Información de conexión y **Abrir Portal de administración de ACS**.
5. En el Portal, debajo de **Configuración del servicio**, seleccione **Identidades del servicio**. De este modo, se muestra su identidad de servicio, que es el valor del nombre de emisor del servicio de control de acceso. Seleccione el vínculo de su identidad de servicio para ver la contraseña, que es el valor de la clave de emisor. Sus valores se pueden copiar.<br/><br/> Por ejemplo, en **Identidades del servicio**, podrá ver "owner". "Owner" es el nombre de emisor de su servicio de control de acceso. Cuando haga clic en el vínculo "owner", verá **Password**. Cuando haga clic en el vínculo "Password", verá el valor. Este valor de Password es su clave de emisor del servicio de control de acceso.  

En resumen: Nombre de emisor = Nombre de identidad de servicio Clave de emisor = Valor de contraseña

En el panel de navegación izquierdo también puede seleccionar **Active Directory** para recuperar los valores de control de acceso.

> [AZURE.IMPORTANT] Cuando se crea un espacio de nombres del servicio de control de acceso usando **Active Directory**, **no** se crea automáticamente una identidad de servicio. Cuando aprovisiona un servicio de BizTalk, se crean automáticamente un espacio de nombres del servicio de control de acceso, una identidad de servicio llamada "owner" (nombre de emisor), una contraseña (clave de emisor) y una clave simétrica.<br /> [Uso del servicio de administración de ACS para configurar identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942) proporciona más información acerca de las identidades del Servicio de control de acceso.


## Nombre de emisor y clave de emisor del bus de servicio
Los servicios de adaptador de BizTalk usan el nombre y la clave de emisor de Bus de servicio. En su proyecto de los servicios de BizTalk en Visual Studio, se usan los servicios de adaptador de BizTalk para conectarse a un sistema local de línea de negocio (LOB). Para conectarse, debe crear el relé de LOB y especificar los detalles de su sistema de LOB. Para ello, debe especificar el nombre y la clave de emisor del bus de servicio.

### Recuperación del nombre y la clave de emisor del bus de servicio

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, haga clic en **Bus de servicio**.
3. Seleccione su espacio de nombres. Seleccione **Información de conexión** en la barra de tareas. De este modo se muestra el **Emisor predeterminado** (nombre de emisor) y la **Clave predeterminada** (clave de emisor). Sus valores se pueden copiar.  

En resumen:  
Nombre de emisor = Emisor predeterminado  
Clave de emisor = Clave predeterminada

## Pasos siguientes
Otros temas acerca de los servicios de BizTalk de Azure:

-  [Instalación del SDK de Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriales: Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Otras referencias
-  [Procedimientos: uso del servicio de administración de ACS para configurar identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=AcomDC_0302_2016-->
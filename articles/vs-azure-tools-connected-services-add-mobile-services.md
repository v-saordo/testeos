<properties 
   pageTitle="Adición de servicios móviles con Servicios conectados en Visual Studio | Microsoft Azure"
   description="Adición de Servicios móviles mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# Adición de Servicios móviles mediante Servicios conectados de Visual Studio

Con Visual Studio 2015, puede conectarse a Servicios móviles de Azure usando el cuadro de diálogo **Agregar servicio conectado**. Puede conectarse desde cualquier aplicación cliente de C#, cualquier aplicación de JavaScript o cualquier aplicación de Cordova multiplataforma. Una vez conectado, puede crear y tener acceso a datos, crear API personalizadas y trabajos programados o agregar compatibilidad con notificaciones de inserción. La operación de servicios conectados agrega todas las referencias adecuadas y el código de conexión. También puede aprovechar la compatibilidad integrada para la autenticación con diferentes esquemas de identidad populares, como cuentas de Microsoft, Azure AD, Facebook y Twitter.

## Tipos de proyecto compatibles

>[AZURE.NOTE] En Visual Studio 2015, la adición de Servicios móviles de Azure a proyectos universales de Windows (Windows 10) con el cuadro de diálogo Agregar servicios conectados no es compatible. Puede agregar Servicios móviles de Azure instalando los paquetes adecuados con el Administrador de paquetes NuGet de su proyecto.

Puede usar el cuadro de diálogo Servicios conectados para conectarse a Servicios móviles de Azure en los siguientes tipos de proyecto.

- Proyectos de la Tienda Windows 8.1 de .NET, Phone y aplicación universal

- Proyectos de la Tienda Windows 8.1 de JavaScript, Phone y aplicación universal

- Proyectos creados con herramientas de Visual Studio para Apache Cordova


## Conectarse a Servicios móviles de Azure con el cuadro de diálogo Agregar servicios conectados

1. Asegúrese de que tiene una cuenta de Azure. Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Abra el cuadro de diálogo **Agregar servicios conectados**.
 - En las aplicaciones .NET, abra el proyecto en Visual Studio, abra el menú contextual del nodo **Referencias** en el Explorador de soluciones y luego elija **Agregar servicio conectado**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - En los proyectos de aplicación Apache Cordova, abra el proyecto en Visual Studio, abra el menú contextual del nodo del proyecto en el Explorador de soluciones y luego elija **Agregar servicio conectado**.

1. En el cuadro de diálogo **Agregar servicio conectado**, seleccione **Servicios móviles de Azure** y elija el botón **Configurar**. Es posible que se le pida que inicie sesión en Azure si aún no lo ha hecho.

    ![Adición de un servicio móvil de Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. En el cuadro de diálogo **Servicios móviles de Azure**, seleccione un servicio móvil existente si tiene uno. Si necesita crear un servicio móvil de Azure, siga el procedimiento indicado a continuación para ello. Si no, continúe con el paso siguiente.

    Para crear una cuenta de servicio móvil:
    1. elija el vínculo **Creación de un servicio** de la parte inferior del cuadro de diálogo.
        ![Agregar nuevo servicio conectado móvil](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. En el cuadro de diálogo **Crear servicio móvil**, puede elegir un servicio móvil de back-end de JavaScript o bien un servicio móvil de back-end de .NET de la lista desplegable **Tiempo de ejecución**. 
  
        ![Creación de un servicio móvil](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        Un servicio de back-end de JavaScript es sencillo y eficaz. Si crea un servicio móvil de back-end de JavaScript, el código JavaScript del servidor se almacena en la nube, pero pueden editarse scripts del servidor mediante el Explorador de servidores o el Portal de administración de Azure.

        Un servicio móvil de back-end de .NET le ofrece todo el potencial y flexibilidad de API web y Entity Framework. Si crea un servicio móvil de back-end de .NET, se crea un proyecto para usted y se agrega a su solución.

    1. Elija la **Región** donde desea el servicio móvil y escriba un nombre de usuario y una contraseña para el servidor.
 
    1. Después de especificar toda la información necesaria, elija el botón **Crear** para crear el servicio móvil.
    2. El nuevo servicio móvil debe aparecer en la lista de servicios del cuadro de diálogo **Servicios móviles de Azure**. Elija el nuevo servicio móvil en la lista y luego el botón **Agregar** para agregar el servicio al proyecto.
    

1. Revise la página Introducción que aparece y consulte cómo se ha modificado el proyecto. Una página Introducción aparece en el explorador siempre que se agregue un servicio conectado. Puede revisar los pasos siguientes sugeridos y los ejemplos de código, o pasar a la página ¿Qué ha ocurrido? para ver qué referencias se agregaron al proyecto y cómo se han modificado los archivos de configuración y códigos.

1. Con los ejemplos de código como guía, comience a escribir código para tener acceso a su servicio móvil.

## ¿Cómo se modifica el proyecto?

El modo en que Visual Studio modifica su proyecto depende del tipo de proyecto. En las aplicaciones cliente de C#, consulte [¿Qué ha ocurrido? - Proyectos de C#](http://go.microsoft.com/fwlink/p/?LinkId=513119). En las aplicaciones cliente de JavaScript, consulte [¿Qué ha ocurrido? - Proyectos de JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). En las aplicaciones de Cordova, consulte [¿Qué ha ocurrido? - Proyectos de Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##Pasos siguientes

Formule preguntas y obtenga ayuda:

 - [Foro de MSDN: Servicios móviles de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Servicios móviles de Azure en el blog del equipo de Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)

 - [Servicios móviles de Azure en azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)

 - [Documentación de Servicios móviles de Azure en azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->
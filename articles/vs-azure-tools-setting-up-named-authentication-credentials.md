<properties
   pageTitle="Configuración de credenciales de autenticación con nombre | Microsoft Azure"
   description="Aprenda a proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes a Azure para publicar una aplicación en Azure desde Visual Studio o para supervisar un servicio en la nube existente."
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
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Configuración de credenciales de autenticación con nombre

Para publicar una aplicación en Azure desde Visual Studio o supervisar un servicio en la nube existente, debe proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes a Azure. Existen varios lugares en Visual Studio donde puede iniciar sesión para proporcionar estas credenciales. Por ejemplo, desde el Explorador de servidores, abra el menú contextual para el nodo **Azure** y elija **Conectar a Azure**. Al iniciar sesión, la información de suscripción asociada con su cuenta de Azure está disponible en Visual Studio y con eso basta.

Azure Tools también admite una forma anterior de proporcionar credenciales, mediante el archivo de suscripción (archivo .publishsettings). En este tema se describe este método, que todavía se admite en el SDK 2.2 de Azure.

Para efectuar la autenticación en Azure se requieren los siguientes elementos:

- Su Id. de suscripción

- Un certificado X.509 v3 válido

>[AZURE.NOTE]La longitud de la clave del certificado X.509 v3 debe tener al menos 2048 bits. Azure rechazará cualquier certificado que no cumpla este requisito o que no sea válido.

Visual Studio usa el Id. de suscripción junto con los datos del certificado como credenciales. Se hace referencia a las credenciales adecuadas en el archivo de suscripción (archivo .publishsettings), que contiene una clave pública para el certificado. El archivo de suscripción puede contener credenciales para más de una suscripción.

Puede editar la información de suscripción en el cuadro de diálogo **Nueva suscripción/Editar suscripción**, como se explica más adelante en este tema.

Si desea crear un certificado por sí mismo, puede consultar las instrucciones de [Crear y cargar un certificado de administración para Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) y después cargar manualmente el certificado en el Portal de administración.

>[AZURE.NOTE]Estas credenciales que Visual Studio necesita para administrar sus servicios en la nube no coinciden con las necesarias para autenticar una solicitud en los servicios de almacenamiento de Azure.

## Modificación o exportación de las credenciales de autenticación en Visual Studio

También puede configurar, modificar o exportar las credenciales de autenticación en el cuadro de diálogo **Nueva suscripción**, que aparece si se lleva a cabo cualquiera de las siguientes acciones:

- En el **Explorador de servidores**, abra el menú contextual para el nodo **Azure**, elija **Administrar suscripciones**, elija la pestaña **Certificados** y después elija el botón **Nuevo** o **Editar**.

- Cuando publique un servicio en la nube de Azure desde el asistente **Publicar aplicación de Azure**, seleccione **Administrar** en la lista **Elija su suscripción**, elija la pestaña Certificados y después haga clic en el botón **Nuevo** o **Editar**.

En el siguiente procedimiento, se da por supuesto que el cuadro de diálogo **Nueva suscripción** está abierto.

### Configuración de las credenciales de autenticación en Visual Studio

1. En la lista **Seleccione un certificado existente para la autenticación**, elija un certificado.

1. Elija el botón **Copiar la ruta de acceso completa**. La ruta de acceso para el certificado (archivo .cer) se copia en el Portapapeles.

    >[AZURE.IMPORTANT]Para publicar la aplicación de Azure desde Visual Studio, debe cargar este certificado en el Portal de administración.

1. Para cargar el certificado en el Portal de administración de Azure:

    1. Elija el vínculo Portal de Azure.

         A continuación, se abrirá el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

    1. Inicie sesión en el Portal de administración de Azure con la cuenta Microsoft y después elija el botón **Servicios en la nube**.

    1. Elija el servicio en la nube que le interese.

        Se abre la página para el servicio.

    1. En la pestaña **Certificados**, elija el botón **Cargar**.

    1. Pegue la ruta de acceso completa del archivo .cer que acaba de crear y después escriba la contraseña que especificó.

<!---HONumber=AcomDC_1223_2015-->
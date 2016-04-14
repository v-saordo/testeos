<properties 
   pageTitle="Acceso a nubes privadas de Azure con Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo tener acceso a recursos de nube privada mediante Visual Studio."
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

# Acceso a nubes privadas de Azure con Visual Studio

##Información general

De forma predeterminada, Visual Studio admite extremos REST de nube pública de Azure. Aunque esto puede ser un problema si se usa Visual Studio en una nube privada de Azure. Puede usar certificados para configurar Visual Studio para tener acceso a extremos REST de nube privada de Azure. Puede obtener estos certificados a través del archivo de configuración de publicación de Azure.

## Para obtener acceso a una nube privada de Azure en Visual Studio

1. En el Portal de administración de la nube privada, descargue el archivo de configuración de publicación o póngase en contacto con su administrador para obtenerlo. En la versión pública de Azure, el vínculo para descargarlo es [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (El archivo que descargue debe tener una extensión .publishsettings).

1. En **Explorador de servidores** en Visual Studio, elija el nodo **Azure** y, en el menú contextual, elija el comando **Administrar suscripciones**.

    ![Comando Administrar suscripciones](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. En el cuadro de diálogo **Administrar suscripciones de Microsoft Azure**, elija la pestaña **Certificados** y luego el botón **Importar**.

    ![Importación de certificados de Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. En el cuadro de diálogo **Importar suscripciones de Microsoft Azure**, vaya a la carpeta en la que guardó el archivo de configuración de publicación y elija el botón **Importar**. Con ello importa los certificados del archivo de configuración de publicación a Visual Studio. Ahora debe poder interactuar con los recursos de nube privada.

    ![Importación de la configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Pasos siguientes

[Publicación en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Descarga e importación de la configuración de publicación y la información de suscripción] (https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=AcomDC_1223_2015-->
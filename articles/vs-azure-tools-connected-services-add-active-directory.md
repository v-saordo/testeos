<properties 
   pageTitle="Adición de Azure Active Directory con Servicios conectados en Visual Studio | Microsoft Azure"
   description="Adición de Azure Active Directory mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="visual-studio-online"
   ms.date="12/16/2015"
   ms.author="tarcher" />

# Adición de Azure Active Directory con Servicios conectados en Visual Studio 

##Información general
Mediante el uso de Azure Active Directory (Azure AD), puede admitir el inicio de sesión único (SSO) para aplicaciones web ASP.NET MVC o la autenticación de Active Directory en los servicios de API web. Con la autenticación de Azure AD, sus usuarios pueden usar sus cuentas desde Azure AD para conectarse a sus aplicaciones web. Las ventajas de la autenticación de Azure AD con la API web incluyen seguridad de datos mejorada al exponer una API desde una aplicación web. Con Azure AD, no es necesario que administre un sistema de autenticación independiente con su propia administración de cuentas y usuarios.

## Tipos de proyecto compatibles

Puede usar el cuadro de diálogo Servicios conectados para conectarse a Azure AD en los siguientes tipos de proyecto.

- Proyectos de ASP.NET MVC

- Proyectos de la API web de ASP.NET


### Conexión con Azure AD mediante el cuadro de diálogo Servicios conectados

1. Asegúrese de que tiene una cuenta de Azure. Si aún no tiene ninguna, puede registrarse para una [evaluación gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. En Visual Studio, abra el menú contextual del nodo **Referencias** en su proyecto y elija **Agregar servicios conectados**.
1. Seleccione **Autenticación de Azure AD** y luego elija **Configurar**.

    ![Elija Agregar autenticación de Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. En la primera página de **Configurar autenticación de Azure AD**, marque **Configure Single Sign-on using Azure AD**.

    Si su proyecto está configurado con otra configuración de autenticación, el asistente le advertirá de que al continuar se deshabilitará la configuración anterior.

    ![Configurar Azure AD en el asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  En la segunda página, seleccione un dominio en la lista desplegable **Dominio**. La lista de dominios contiene todos los dominios a los que pueden tener acceso las cuentas enumeradas en el cuadro de diálogo Configuración de cuenta. Como alternativa, puede escribir un nombre de dominio si no encuentra el que busca, por ejemplo, mydomain.onmicrosoft.com. Puede elegir la opción para crear una nueva aplicación de Azure AD o utilizar la configuración de una aplicación de Azure AD existente.

    ![Configurar Azure AD en el asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. En la tercera página del asistente, asegúrese de que **Leer datos de directorio** está activada. El asistente rellenará el **secreto de cliente**.

    ![Configurar Azure AD en el asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Elija el botón **Finalizar**. El cuadro de diálogo agrega el código de configuración y las referencias necesarios para habilitar su proyecto para la autenticación de Azure AD. Puede ver el dominio de AD en el Portal de Azure.

1. Revise la página Introducción que aparece en el explorador para obtener ideas sobre los siguientes pasos y la página ¿Qué ha ocurrido? para ver cómo se modificó el proyecto. Si quiere comprobar que todo funcionó, abra uno de los archivos de configuración modificados y compruebe que la configuración mencionada en ¿Qué ha ocurrido? existe. Por ejemplo, al archivo web.config principal de un proyecto de ASP.NET MVC se le agregará esta configuración:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## ¿Cómo se modifica el proyecto?

Al ejecutar el asistente, Visual Studio agrega Azure AD y referencias asociadas a su proyecto. También se modifican los archivos de configuración y archivos de código de su proyecto para agregar compatibilidad con Azure AD. Las modificaciones específicas realizadas por Visual Studio dependen del tipo de proyecto. Para información detallada sobre cómo se modifican los proyectos de ASP.NET MVC, consulte [¿Qué ha ocurrido? - Proyectos de MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para los proyectos de API web, consulte [¿Qué ha ocurrido? - Proyectos de API web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##Pasos siguientes

Formule preguntas y obtenga ayuda.

 - [Foro de MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentación de Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Entrada de blog: introducción a Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_0128_2016-->
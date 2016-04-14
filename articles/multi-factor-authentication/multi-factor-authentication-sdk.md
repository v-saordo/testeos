<properties 
	pageTitle="Integración de las identidades locales con Azure Active Directory." 
	description="Aquí se describe qué es Azure AD Connect y por qué debería usarlo." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Creación de Multi-Factor Authentication en aplicaciones personalizadas (SDK)

El Kit de desarrollo de Software (SDK) de Azure Multi-Factor Authentication le permite generar verificaciones de llamadas telefónicas y mensajes de texto directamente en los procesos de inicio de sesión o transacción de las aplicaciones de su inquilino de Azure AD.

El SDK de Multi-Factor Authentication está disponible para C#, Visual Basic (.NET), Java, Perl, PHP y Ruby. El SDK proporciona un contenedor fino en torno a la autenticación multifactor. Incluye todo lo que necesita para escribir su código, incluidos los archivos de código fuente comentados, los archivos de ejemplo y un archivo Léame detallado. Cada SDK también incluye un certificado y una clave privada para cifrar transacciones que es único para su proveedor de Multi-Factor Authentication. Siempre y cuando tenga un proveedor, puede descargar el SDK en tantos idiomas y formatos como necesite.

La estructura de las API del SDK de Multi-Factor Authentication es bastante sencilla. Crea una llamada de función única a una API con los parámetros de la opción de varios factores, como el modo de comprobación y los datos de usuario, como el número de teléfono al que llamar o el número PIN que validar. Las API convierten la llamada de función en solicitudes de servicios web al servicio Azure Multi-Factor Authentication basado en la nube. Todas las llamadas deben incluir una referencia al certificado privado que se incluye en todos los SDK.

Dado que las API no tienen acceso a los usuarios registrados en Azure Active Directory, debe proporcionar información de usuario, como números de teléfono y códigos PIN, en un archivo o una base de datos. Además, las API no proporcionan funciones de administración de inscripciones o usuarios, por lo que necesitará crear estos procesos en la aplicación.






## Descarga del SDK de Azure Multi-Factor Authenticationication 

Hay dos maneras diferentes de descargar el SDK de Azure Multi-Factor Authentication. Ambas se pueden hacer a través del Portal de Azure. La primera es administrando el proveedor de autenticación multifactor directamente. La segunda es mediante la configuración del servicio. La segunda opción requiere un proveedor de autenticación multifactor o una licencia de Azure MFA, Azure AD Premium o Enterprise Mobility Suite.


### Para descargar el SDK de Azure Multi-Factor Authentication del Portal de Azure.


1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior de la página Active Directory, haga clic en **Proveedores de autenticación multifactor**.
4. Haga clic en **Administrar** en la parte inferior.
5. De este modo se abrirá una nueva página. A la izquierda, en la parte inferior, haga clic en SDK.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga asociados.
7. Guarde el archivo descargado.



### Descarga del SDK de Azure Multi-Factor Authentication mediante la configuración del servicio


1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. Haga doble clic en la instancia de Azure AD.
4. En la parte superior, haga clic en **Configurar**.
5. En Multi-Factor Authentication, seleccione **Administrar configuración del servicio** ![Descargar](./media/multi-factor-authentication-sdk/download2.png)
6. En la página de configuración de servicios, en la parte inferior de la pantalla, haga clic en **Ir al portal**. ![Descargar](./media/multi-factor-authentication-sdk/download3a.png)
7. De este modo se abrirá una nueva página. A la izquierda, en la parte inferior, haga clic en SDK.
8. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga asociados.
9. Guarde el archivo descargado.

## Contenido del SDK de Azure Multi-Factor Authentication
En el SDK encontrará los siguientes elementos:

- **LÉAME**. Explica cómo usar las API de Multi-Factor Authentication en una aplicación nueva o existente.
- **Archivos de origen** para la Multi-Factor Authentication
- **Certificado de cliente** que se usa para comunicarse con el servicio Multi-Factor Authentication
- **Clave privada** para el certificado
- **Resultados de la llamada.** Una lista de códigos de resultado de la llamada. Para abrir este archivo, use una aplicación con formato de texto, como WordPad. Use los códigos de resultado de la llamada para probar y solucionar problemas de la implementación de Multi-Factor Authentication en su aplicación. No son códigos de estado de autenticación.
- **Ejemplos.** Código de ejemplo para una implementación básica de funcionamiento de Multi-Factor Authentication.


>[AZURE.WARNING]El certificado de cliente es un certificado privado único que se generó especialmente para usted. No comparta ni pierda este archivo. Es su clave para garantizar la seguridad de las comunicaciones con el servicio Multi-Factor Authentication.

## Ejemplo de código: comprobación del teléfono de modo estándar

Este ejemplo de código muestra cómo usar las API en el SDK de Azure Multi-Factor Authentication para agregar comprobación de llamadas de voz de modo estándar a la aplicación. El modo estándar es una llamada de teléfono a la que el usuario responde presionando la tecla #.

Este ejemplo usa el SDK Multi-Factor Authentication C# .NET 2.0 en una aplicación ASP.NET básica con lógica de servidor de C#, pero el proceso es muy similar para implementaciones sencillas en otros idiomas. Dado que el SDK incluye archivos de origen, no archivos ejecutables, puede generar los archivos y hacer referencia a ellos o incluirlos directamente en la aplicación.

>[AZURE.NOTE]Al implementar la Multi-Factor Authentication, use factores adicionales como la comprobación secundaria o terciaria para complementar el método de autenticación principal. Estos métodos no están diseñados para usarse como métodos de autenticación principal.

### Información general del ejemplo de código
Este código de ejemplo para una aplicación de demostración web muy sencilla usa una llamada telefónica con una respuesta de clave # para completar la autenticación del usuario. Este factor de llamada de teléfono se conoce en la Multi-Factor Authentication como modo estándar.

El código de cliente no incluye ningún elemento específico de Multi-Factor Authentication. Dado que los factores de autenticación adicionales son independientes de la autenticación principal, puede agregarlos sin cambiar la interfaz de inicio de sesión existente. Las API del SDK multifactor le permiten personalizar la experiencia del usuario, pero es posible que no tenga que cambiar nada en absoluto.

El código de servidor agrega autenticación de modo estándar en el paso 2. Crea un objeto PfAuthParams con los parámetros necesarios para la comprobación de modo estándar: nombre de usuario, número de teléfono y modo, y la ruta de acceso al certificado de cliente (CertFilePath), que es necesario en cada llamada. Para obtener una demostración de todos los parámetros en PfAuthParams, consulte el archivo de ejemplo en el SDK.

A continuación, el código pasa el objeto PfAuthParams a la función pf\_authenticate(). El valor devuelto indica el éxito o fracaso de la autenticación. Los parámetros de salida, callStatus y errorID, contienen información del resultado de la llamada adicional. Los códigos de resultado de la llamada se documentan en el archivo de resultados de la llamada en el SDK.

Esta implementación mínima puede escribirse en tan solo unas pocas líneas. Sin embargo, en el código de producción, incluiría un tratamiento de errores más sofisticado, código de la base de datos adicional y una experiencia de usuario mejorada.

### Código de cliente web

A continuación se facilita código de cliente web para una página de demostración.

	
	<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>
	
	<!DOCTYPE html>
	
	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	<title>Multi-Factor Authentication Demo</title>
	</head>
	<body>
	<h1>Azure Multi-Factor Authentication Demo</h1>
	<form id="form1" runat="server">
	
	<div style="width:auto; float:left">
	Username:&nbsp;<br />
	Password:&nbsp;<br />
	</div>
	
	<div">
	<asp:TextBox id="username" runat="server" width="100px"/><br />
	<asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
	</div>
	
	<asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>
	
	<p><asp:Label ID="lblResult" runat="server"></asp:Label></p>
	
	</form>
	</body>
	</html>


### Código de servidor

En el siguiente código de servidor, Multi-Factor Authentication se configura y ejecuta en el paso 2. El modo estándar (MODE\_STANDARD) es una llamada de teléfono a la que el usuario responde presionando la tecla #.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.UI;
	using System.Web.UI.WebControls;
	
	public partial class _Default : System.Web.UI.Page
	{
	    protected void Page_Load(object sender, EventArgs e)
	    {
	    }
	
	    protected void btnSubmit_Click(object sender, EventArgs e)
	    {
	        // Step 1: Validate the username and password
	        if (username.Text != "Contoso" || password.Text != "password")
	        {
	            lblResult.ForeColor = System.Drawing.Color.Red;
	            lblResult.Text = "Username or password incorrect.";
	        }
	        else
	        {
	            // Step 2: Perform multi-factor authentication
	
	            // Add call details from the user database.
	            PfAuthParams pfAuthParams = new PfAuthParams();
	            pfAuthParams.Username = username.Text;
	            pfAuthParams.Phone = "9134884271";
	            pfAuthParams.Mode = pf_auth.MODE_STANDARD;
	            
	            // Specify a client certificate 
	            // NOTE: This file contains the private key for the client
	            // certificate. It must be stored with appropriate file 
	            // permissions.
	            pfAuthParams.CertFilePath = "c:\\cert_key.p12";
	
	            // Perform phone-based authentication
	            int callStatus;
	            int errorId;
	
	            if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
	            {
	                lblResult.ForeColor = System.Drawing.Color.Green;
	                lblResult.Text = "Multi-Factor Authentication succeeded.";
	            }
	            else
	            {
	                lblResult.ForeColor = System.Drawing.Color.Red;
	                lblResult.Text = " Multi-Factor Authentication failed.";
	            }
	        }
	
	    }
	}

<!---HONumber=AcomDC_0218_2016-->
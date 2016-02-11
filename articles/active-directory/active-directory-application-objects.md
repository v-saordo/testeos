<properties
   pageTitle="Objetos Application y objetos ServicePrincipal | Microsoft Azure"
   description="Una descripción de la relación entre los objetos Application y ServicePrincipal de Azure Active Directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>


# Objetos Application y objetos ServicePrincipal

Este diagrama ilustra la relación entre un objeto Application y un objeto ServicePrincipal en el contexto de una aplicación de ejemplo denominada **HR app**. Hay tres inquilinos: **Adatum**, el inquilino que desarrolla la aplicación, y **Contoso** y **Fabrikam**, los inquilinos que consumen la aplicación **HR app**.

![Relación entre un objeto Application y un objeto ServicePrincipal](./media/active-directory-application-objects/application-objects-relationship.png)


Al registrar una aplicación en el Portal de administración de Azure, se crean dos objetos en el inquilino del directorio:

- **Objeto Application**: este objeto representa una definición de la aplicación. Puede encontrar una descripción detallada de sus propiedades en la sección **Objeto Application** más adelante.

- **Objeto ServicePrincipal**: este objeto representa una instancia de su aplicación en el inquilino del directorio. Puede aplicar directivas a objetos ServicePrincipal, incluida la asignación de permisos a ServicePrincipal que permiten que la aplicación lea los datos del directorio de su inquilino. Cada vez que cambie el objeto Application, los cambios también se aplicarán al objeto ServicePrincipal asociado en el inquilino.


> [AZURE.NOTE]Si la aplicación está configurada para el acceso externo, los cambios realizados en el objeto Application no se reflejan en el ServicePrincipal del inquilino consumidor hasta que el inquilino consumidor quite el acceso y lo conceda de nuevo.



En el diagrama anterior, el paso "1" es el proceso de creación de los objetos Application y ServicePrincipal.

En el paso 2, cuando el administrador de una empresa concede acceso, se crea un objeto ServicePrincipal en el inquilino de Azure AD de la empresa y se le asigna el nivel de acceso de directorio que concedió el administrador de la empresa.

En el paso 3, los inquilinos consumidores de una aplicación (como Contoso y Fabrikam) tienen cada uno su propio objeto ServicePrincipal que representa la instancia de la aplicación. En este ejemplo, cada uno de ellos tiene un ServicePrincipal que representa a la aplicación HR app.





## Propiedades del objeto Application

En las siguientes tablas se enumeran todas las propiedades de un objeto Application y se incluyen detalles importantes para los desarrolladores. Estas propiedades se aplican a las aplicaciones web, las API web y las aplicaciones cliente nativas que se registran con Azure AD.


### General

Propiedad | Descripción
| ------------- | -----------
| Nombre | Nombre para mostrar de la aplicación. Propiedad requerida en el asistente para **agregar aplicación**.
| Logotipo | Un logotipo de aplicación que representa a la aplicación o la empresa. Este logotipo permite a los usuarios externos asociar más fácilmente la solicitud de concesión de acceso con la aplicación. Al cargar un logotipo, respete las especificaciones del asistente para **cargar logotipo**. Si no se proporciona un logotipo, aparece un logotipo predeterminado.
| Acceso externo | Determina si los usuarios de las organizaciones externas pueden conceder el inicio de sesión único en la aplicación y el acceso a datos en el directorio de su organización. Este control repercute solo en la capacidad de conceder acceso. No cambia el acceso que ya se ha concedido. Los administradores de la empresa son los únicos que pueden conceder acceso.


### Inicio de sesión único

Propiedad | Descripción
| ------------- | -----------
| URI de identificador de aplicación | Un identificador lógico único para la aplicación. Propiedad requerida en el asistente para **agregar aplicación**. <br><br>Dado que el URI de identificador de aplicación es un identificador lógico, no necesita resolverse en una dirección de Internet. La aplicación lo presenta al enviar una solicitud de inicio de sesión único a Azure AD. Azure AD identifica la aplicación y envía la respuesta de inicio de sesión (un token SAML) a la dirección URL de respuesta que se proporcionó durante el registro de la aplicación. Utilice el valor de URI de identificador de aplicación para establecer la propiedad wtrealm (para WS-Federation) o la propiedad Issuer (de SAML-P) al realizar una solicitud de inicio de sesión. El **URI de identificador de aplicación** debe ser un valor único en Azure AD en su organización.<br><br>**Nota**: al habilitar una aplicación para los usuarios externos, el valor del URI de identificador de aplicación debe ser una dirección en uno de los dominios comprobados de su directorio. Por lo tanto, no puede ser un URN. Esta medida de seguridad impide a otras organizaciones especificar (y tomar) una propiedad única que pertenezca a su organización. Durante el desarrollo, puede cambiar el URI de identificador de aplicación a una ubicación en el dominio inicial de su organización (si no ha comprobado un dominio personalizado o personal) y actualizar la aplicación para utilizar este nuevo valor. El dominio inicial es el dominio de nivel 3 que crea durante el registro; por ejemplo, contoso.onmicrosoft.com.
| Dirección URL de la aplicación | La dirección de una página web donde los usuarios pueden iniciar sesión y utilizar la aplicación. Propiedad requerida en el asistente para **agregar aplicación**.<br><BR>**Nota**: el valor establecido para esta propiedad en el asistente para agregar aplicación también se establece como el valor de la dirección URL de respuesta.
| URL de respuesta | La dirección física de la aplicación. Azure AD envía un token con la respuesta de inicio de sesión único a esta dirección. Durante el primer registro en el asistente para **agregar aplicación**, el valor establecido para la dirección URL de la aplicación también se establece como el valor de la dirección URL de respuesta. Al realizar una solicitud de inicio de sesión, use el valor de la dirección URL de respuesta para establecer la propiedad wreply (de WS-Federation) o la propiedad **AssertionConsumerServiceURL** (para SAML-P).<br><BR>**Nota**: al habilitar una aplicación para los usuarios externos, la dirección URL de respuesta debe ser una dirección **https://**. | Dirección URL de metadatos de federación | (Opcional). Representa la dirección URL física del documento de metadatos de federación para su aplicación. Es un valor requerido para admitir el cierre de sesión de SAML-P. Azure AD descarga el documento de metadatos que se hospeda en este extremo y lo utiliza para detectar la parte pública del certificado que se utiliza para comprobar la firma de las solicitudes de cierre de sesión y la dirección URL de cierre de sesión de la aplicación. No puede configurar esta propiedad cuando se agrega primero la aplicación. Solo se pueden configurarse posteriormente.<br><BR>**Nota**: si necesita admitir el cierre de sesión de SAML-P, pero no tiene un extremo de metadatos de federación para su aplicación, póngase en contacto con el soporte al cliente para ver otras opciones.


### Llamada a la API Graph o las API web

Propiedad | Descripción
| ------------- | -----------
| Id. de cliente | El identificador único para la aplicación. Deberá utilizar este identificador en las llamadas a la API Graph u otras API web registradas en Azure AD. Azure AD genera automáticamente este valor durante el registro de la aplicación y no se puede cambiar.<BR><BR>Para permitir que la aplicación tenga acceso al directorio (acceso de lectura y escritura) a través de la API Graph, necesita un identificador de cliente y una clave (conocidos en OAuth 2.0 como un secreto de cliente). La aplicación utiliza el identificador de cliente y la clave para solicitar un token de acceso desde el extremo del token de OAuth 2.0 de Azure AD. (Para ver todos los extremos de Azure AD, en la barra de comandos, haga clic en **Ver extremos**). Al usar la API Graph para obtener o establecer (cambiar) datos de directorio, la aplicación usa este token de acceso en el encabezado de autorización de la solicitud a la API Graph.
| Claves | Si la aplicación lee o escribe datos en Azure AD, como los datos que pasan a estar disponibles a través de la API Graph, la aplicación necesita una clave. Cuando se solicita un token de acceso para llamar a la API Graph, la aplicación proporciona el **identificador de cliente** y la **clave**. El extremo de token utiliza el identificador y la clave para autenticar la aplicación antes de emitir el token de acceso. Puede crear varias claves para tratar los escenarios de sustitución de claves. Y puede eliminar las claves que han caducado, están en riesgo o ya no se utilizan.
| Administración del acceso | Elija uno de los tres diferentes niveles de acceso: inicio de sesión único (SSO), SSO y lectura de datos de directorio o SSO y lectura/escritura de datos de directorio. También puede quitar el acceso. <br><BR>** Nota **: los cambios realizados en el nivel de acceso de directorio de la aplicación solo se aplican a su directorio. Los cambios no se aplican a los clientes a los que se haya concedido acceso a la aplicación.


### Clientes nativos

Propiedad | Descripción
| ------------- | -----------
| URI de redireccionamiento | Se trata del URI al que Azure AD redirigirá al usuario-agente en respuesta a una solicitud de autorización OAuth 2.0. El valor no tiene que ser un extremo físico, pero debe ser un URI válido.

##

<!---HONumber=AcomDC_0114_2016-->
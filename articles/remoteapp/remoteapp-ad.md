
<properties 
    pageTitle="Requisitos de Azure AD + Active Directory para Azure RemoteApp | Microsoft Azure" 
    description="Aprenda a configurar Active Directory para trabajar con RemoteApp de Azure." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Requisitos de Azure AD + Active Directory para Azure RemoteApp



En el caso de la colección de híbrida de Azure RemoteApp o de una colección en la nube que desee federar mediante AD Connect, deberá hacer lo siguiente.

### Conexión de Azure AD y Active Directory

Si desea conectar el inquilino de Azure AD y los entornos de Active Directory locales, utilice AD Connect. En tan solo [4 clics](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) habrá conectado los dos directorios.

Nota: la sincronización de directorios se requiere para las colecciones híbridas.

### Comprobación de la coincidencia de su "@dominio.com"
Antes de empezar, asegúrese de que el UPN del bosque local coincide con el sufijo de su dominio de Azure AD.

Una vez configurado el sufijo del dominio UPN en Azure AD, todos los usuarios que inicien sesión en Azure RemoteApp lo harán como "usuario@<the suffix you set up>". Asegúrese de que los usuarios también pueden iniciar con el mismo user@suffix en el dominio local. En ciertos casos, puede configurar un nombre de dominio en Azure AD al especificar un sufijo de dominio diferente para el usuario local. En este caso, los usuarios no podrán conectarse a los equipos o recursos unidos mediante dominio a través de Azure RemoteApp.

Por ejemplo, si configura el sufijo de dominio UPN en AAD como contoso.com, pero algunos usuarios locales y de Active Directory están configurados para iniciar sesión con @contoso.uk, esos usuarios no podrán iniciar sesión correctamente en la colección ARA. El UPN de los usuarios en AAD y AD deben ser el mismo para que el inicio de sesión sea posible.

### Creación de objetos para Azure RemoteApp
También debe crear los siguientes objetos de Active Directory locales:

- Una cuenta de servicio para proporcionar acceso a los recursos del dominio para los programas de RemoteApp mediante la combinación de extremos RDSH al dominio local.
- Una unidad organizativa (OU) que contenga objetos de equipo de RemoteApp. Se recomienda usar la unidad organizativa (aunque no es necesario) para aislar las cuentas y las directivas que se van a utilizar con RemoteApp.

Se necesitan estos dos objetos cuando se crea la colección de RemoteApp, por lo que debe asegurarse de realizar estos pasos en primer lugar.

<!---HONumber=AcomDC_0114_2016-->
<properties
   pageTitle="Requisitos previos de Catálogo de datos de Azure"
   description="¿Qué es necesario para comenzar a usar el Catálogo de datos de Azure?"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="maroche"/>

# ¿Qué es necesario para comenzar a usar el Catálogo de datos de Azure?

## Requisitos previos de Catálogo de datos de Azure

Hay algunas cosas de las que debe encargarse para poder configurar **Catálogo de datos de Azure**. No se preocupe: no tardará mucho.

## Azure Active Directory

Azure Active Directory (Azure AD) proporciona una manera fácil para la empresa de administrar la identidad y acceso, tanto en la nube y como local. Los usuarios pueden usar una única cuenta profesional o educativa para efectuar el inicio de sesión único en cualquier aplicación web en la nube y local. Catálogo de datos de Azure usa Azure AD para autenticar el inicio de sesión. Para obtener más información, consulte [Introducción al uso de Azure AD](active-directory-get-started.md).

## Configuración de directivas de Active Directory

En ocasiones, es posible que los usuarios se encuentren en una situación en la que puedan iniciar sesión en el portal del Catálogo de datos de Azure, pero cuando intenten iniciar sesión en la herramienta de registro de orígenes de datos reciban un mensaje de error que les impida iniciar sesión. El problema de este comportamiento puede ocurrir solamente cuando el usuario esté en la red de la empresa, o bien solo cuando el usuario se conecte desde fuera de la red de empresa.

La herramienta de registro de orígenes de datos usa la autenticación de formularios para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, la autenticación de formularios debe ser habilitada en la directiva de autenticación global por un administrador de Active Directory.

La directiva de autenticación global permite habilitar los métodos de autenticación de forma independiente para las conexiones de extranet y de intranet, como se indica más adelante. Es posible que se produzcan errores de inicio de sesión si no está habilitada la autenticación de formularios para la red desde la que se conecta el usuario.

 ![Directiva de autenticación global de Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obtener más información, vea [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).


## Suscripción de Azure
Las suscripciones de Azure le ayudan a organizar el acceso a los recursos de servicio en la nube como Catálogo de datos de Azure. También le ayudan a controlar cómo se informa, factura y paga el uso de recursos. Cada suscripción puede tener una configuración de facturación y pago diferente, por lo que puede tener varias suscripciones y planes diferentes por departamento, proyecto, oficina regional, etc. Cada servicio en la nube pertenece a una suscripción, y debe tener una suscripción para poder configurar el Catálogo de datos de Azure. Para obtener más información, vea [Administrar cuentas, suscripciones y roles administrativos](https://msdn.microsoft.com/library/azure/hh531793.aspx).

<!---HONumber=AcomDC_0211_2016-->
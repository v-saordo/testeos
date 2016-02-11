<properties
	pageTitle="Directivas de dispositivo de acceso condicional para servicios de Office 365 | Microsoft Azure"
	description="Detalles sobre cómo las condiciones basadas en el dispositivo controlan el acceso a los servicios de Office 365. Mientras que los trabajadores de la información desean tener acceso a servicios de Office 365 como Exchange y SharePoint Online en el trabajo o en la escuela desde sus dispositivos personales, los administradores de TI desean que el acceso sea seguro. Los administradores de TI pueden aprovisionar directivas de dispositivos de acceso condicional para proteger los recursos corporativos, al mismo tiempo que permiten que los trabajadores de la información con dispositivos compatibles tengan acceso a los servicios."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>
# Directivas de dispositivo de acceso condicional para servicios de Office 365

El término "acceso condicional" conlleva numerosas condiciones, como un usuario autenticado mediante Multi-Factor Authentication, un dispositivo autenticado, un dispositivo compatible, etc. En este tema, nos centraremos principalmente en las condiciones basadas en el dispositivo para controlar el acceso a los servicios de Office 365. Mientras que los trabajadores de la información desean tener acceso a servicios de Office 365 como Exchange y SharePoint Online en el trabajo o en la escuela desde sus dispositivos personales, los administradores de TI desean que el acceso sea seguro. Los administradores de TI pueden aprovisionar directivas de dispositivos de acceso condicional para proteger los recursos corporativos, al mismo tiempo que permiten que los trabajadores de la información con dispositivos compatibles tengan acceso a los servicios. Las directivas de acceso condicional a Office 365 pueden configurarse desde el portal de acceso condicional de Microsoft Intune.

Azure Active Directory aplica las directivas de acceso condicional para proteger el acceso a los servicios de Office 365. Un administrador de inquilinos puede crear una directiva de acceso condicional que impide que un usuario con un dispositivo no compatible tenga acceso a un servicio de Office 365. El usuario debe cumplir las directivas de dispositivos de la empresa para que se le conceda acceso al servicio. Como alternativa, el administrador puede crear una directiva que requiere que los usuarios simplemente inscriban sus dispositivos para tener acceso a un servicio de Office 365. Las directivas pueden aplicarse a todos los usuarios de una organización, o bien limitarse a algunos grupos de destino y mejorarse con el tiempo para incluir otros grupos de destino.

Como requisito previo para la aplicación de directivas de dispositivos, los usuarios deben registrar sus dispositivos con el servicio de registro de dispositivos de Azure Active Directory. Puede optar por habilitar Multi-Factor Authentication (MFA) para registrar los dispositivos con el servicio de registro de dispositivos de Azure Active Directory. Se recomienda el uso de MFA para el servicio de registro de dispositivos de Azure Active Directory. Cuando MFA está habilitado, los usuarios que registran sus dispositivos con el servicio de registro de dispositivos de Azure Active Directory deben someterse a una autenticación de segundo factor.

##¿Cómo funciona la directiva de acceso condicional?

Cuando un usuario solicita acceso a un servicio de Office 365 desde una plataforma de dispositivos compatible, Azure Active Directory autentica el usuario y el dispositivo desde el que el usuario inicia la solicitud, y concede acceso al servicio solo cuando el usuario cumple la directiva establecida para el servicio. Los usuarios cuyos dispositivos no están inscritos reciben instrucciones en las que se les explica cómo inscribirse y ser compatibles para tener acceso a los servicios corporativos de Office 365. Los usuarios de dispositivos iOS y Android deben inscribir sus dispositivos mediante la aplicación del portal de empresa. Cuando un usuario inscribe su dispositivo, este se registra con Azure Active Directory y se inscribe para el cumplimiento de normas y la administración de dispositivos. Los clientes deben usar el servicio de registro de dispositivos de Azure Active Directory junto con Microsoft Intune para habilitar la administración de dispositivos móviles para el servicio de Office 365. La inscripción de dispositivos es un requisito previo para que los usuarios tengan acceso a servicios de Office 365 cuando se aplican directivas de dispositivos.

Cuando un usuario inscribe correctamente su dispositivo, este pasa a ser de confianza. Azure Active Directory ofrece el inicio de sesión único para tener acceso a aplicaciones de empresa y aplica la directiva de acceso condicional para conceder acceso a un servicio no solo la primera vez que el usuario lo solicite, sino cada vez que solicite renovar el acceso. El usuario no tendrá acceso a los servicios cuando se cambien las credenciales de inicio de sesión, cuando pierda o le roben el dispositivo o cuando no se cumpla la directiva en el momento de solicitar una renovación.

## Consideraciones de la implementación:
Debe utilizar el servicio de registro de dispositivos de Azure Active Directory para registrar los dispositivos.

Cuando los usuarios se autentiquen en las instalaciones, se requieren los Servicios de federación de Active Directory (AD FS) (1.0 y superior). Se producirá un error en Multi-Factor Authentication para la unión al área de trabajo cuando el proveedor de identidades no sea capaz de realizar Multi-Factor Authentication. Por ejemplo, AD FS 2.0 no permite Multi-Factor Authentication. El administrador de inquilinos debe asegurarse de que AD FS local permite MFA y de que se ha habilitado un método de MFA válido antes de habilitar MFA en el servicio de registro de dispositivos de Azure Active Directory. Por ejemplo, AD FS en Windows Server 2012 R2 tiene capacidades de MFA. También debe habilitar otro método de autenticación válido (MFA) en el servidor de AD FS antes de habilitar MFA en el servicio de registro de dispositivos de Azure Active Directory. Para obtener más información sobre los métodos de MFA admitidos en AD FS, consulte Configurar métodos de autenticación adicionales de AD FS.

## Preguntas más frecuentes

Q: ¿Qué es la directiva de exclusión predeterminada para plataformas de dispositivos no compatibles?

R: Actualmente, las directivas de acceso condicional se aplican de forma selectiva a los usuarios de dispositivos iOS y Android. De forma predeterminada, las aplicaciones de otras plataformas de dispositivos no se ven afectadas por la directiva de acceso condicional para dispositivos iOS y Android. Sin embargo, el administrador de inquilinos puede invalidar la directiva global para impedir el acceso a los usuarios en plataformas no compatibles. Se ha previsto ampliar la directiva de acceso condicional a los usuarios de otras plataformas, incluido Windows.

P: ¿Cuándo se ampliará la directiva de acceso condicional a los servicios de Office 365 para las aplicaciones basadas en el explorador? (Por ejemplo, OWA, SharePoint basado en el explorador).

R: En este momento, el acceso condicional a los servicios de Office365 se limita a las aplicaciones enriquecidas en el dispositivo. Se ha previsto ampliar la directiva de acceso condicional a los usuarios que tengan acceso a los servicios desde exploradores.

<!---HONumber=AcomDC_1203_2015-->
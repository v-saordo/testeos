<properties
   pageTitle="Comparación de funcionalidades para administrar identidades externas con Azure Active Directory | Microsoft Azure"
   description="Compara la colaboración B2B de Azure Active Directory, B2C y la aplicación multiinquilino para admitir la autenticación y autorización de identidades externas."
   services="active-directory"
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="01/22/2016"
   ms.author="asuthar"/>

# Comparación de funcionalidades para administrar identidades externas con Azure Active Directory

Además de administrar el acceso de los recursos móviles a aplicaciones SaaS, Azure Active Directory (Azure AD) puede ayudar a su organización a compartir recursos con sus socios comerciales y entregar aplicaciones a las empresas y los consumidores.

## Desarrollo de aplicaciones para empresas

¿Ofrece a las empresas algún servicio o aplicación como, por ejemplo, un servicio de nóminas? Azure AD proporciona la plataforma de identidad que le permite crear aplicaciones que se integran perfectamente con millones de organizaciones que ya han configurado Azure AD como parte de la implementación de Office 365 u otros servicios de empresa.

**Ejemplo:** un distribuidor farmacéutico proporciona suministros médicos y sistemas de información para el sector sanitario. Necesitaban implementar una aplicación de análisis para procedimientos médicos y querían que los clientes administrasen sus propias identidades. Esta compañía eligió Azure AD como la plataforma de identidad para su aplicación de administración de procedimientos, proporcionando identidades de Azure AD a sus clientes al suscribirse cuando era necesario.

## Habilitación de acceso de socio comercial a los recursos corporativos

¿Tiene socios comerciales u otros usuarios fuera de la compañía que necesitan acceso a los recursos de la empresa como un sitio de SharePoint o el sistema ERP? Azure AD permite que los administradores concedan a los usuarios externos (que pueden o no existir en Azure AD) acceso de inicio de sesión único a aplicaciones corporativas. Esto mejora la seguridad ya que los usuarios pierden el acceso cuando abandonan la organización del asociado, al tiempo que usted sigue controlando las directivas de acceso dentro de su organización. Esto también simplifica la administración ya que no necesita administrar un directorio de socios externos ni relaciones de federación por socio.

**Ejemplo:** una empresa de creación de imágenes proporciona a los distribuidores servicios de creación de imágenes fotográficas y dirige la red comercial más grande de quioscos de impresión. Eligieron AAD para habilitar miles de usuarios de sus socios comerciales de distribución para que pudieran utilizar sus propias credenciales para descargar los materiales de marketing comercial más recientes y reordenar los suministros de procesamiento fotográfico de la extranet del proveedor de la compañía.

## Desarrollo de aplicaciones para consumidores

¿Necesita publicar de manera segura y rentable aplicaciones en línea como, por ejemplo, una interfaz de tienda, para millones de consumidores? Azure AD proporciona una plataforma que permite el inicio de sesión a través de redes sociales, así como el inicio de sesión con usuario y contraseña, el registro de autoservicio de marca y el autoservicio de restablecimiento de contraseña para los consumidores de la aplicación. Esto aumenta la comodidad para los consumidores al tiempo que reduce la carga de trabajo de sus desarrolladores.

**Ejemplo:** la principal franquicia de deportes en el mundo necesitaba relacionarse directamente con sus 450 millones de aficionados. Para ello, crearon una aplicación móvil con Azure AD para la autenticación y almacenamiento de perfiles de usuario. Los aficionados tienen un registro y un inicio de sesión simplificado mediante el empleo de cuentas sociales como Facebook o pueden utilizar el método tradicional con nombre de usuario y contraseña para poder disfrutar de una experiencia de conexión directa desde teléfonos iOS, Android y Windows. La creación de la plataforma de Azure AD establecida redujo considerablemente el código personalizado al tiempo que proporcionaba a la franquicia una experiencia de marca personalizada y redujo la preocupación sobre seguridad, infracciones de datos y escalabilidad.

## Comparación de funcionalidades de Azure AD

Cada uno de los escenarios que ya se han descrito en este artículo pueden resolverse con funcionalidades de Azure AD. Esta tabla le ayudará a aclarar las funcionalidades más relevantes para usted:

| **Tenga presente este producto...** | Aplicación multiinquilino de Azure AD | Colaboración B2B de Azure AD | Azure AD B2C |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Si tengo que proporcionar...** | un servicio para empresas | acceso de socio a mis aplicaciones | un servicio para consumidores |
| **Y mi empresa se parece a...** | Un distribuidor farmacéutico | Una empresa de creación de imágenes | Una franquicia de deportes |
| **Que implementa una aplicación para...** | Administrar procedimientos | La extranet del proveedor | Aficionados al fútbol |
| **Dirigida a...** | Consultorios médicos | Socios comerciales aprobados | Cualquier persona con correo electrónico |
| **Accesible cuando...** | El administrador del cliente da su consentimiento | Mi administrador le invita | El cliente se registra |

<!---HONumber=AcomDC_0128_2016-->
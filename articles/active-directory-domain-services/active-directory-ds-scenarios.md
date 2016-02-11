<properties
	pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: escenarios de implementación | Microsoft Azure"
	description="Escenarios de implementación de Servicios de dominio de Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>


# Escenarios y casos de uso de implementación
En esta sección, echamos un vistazo a algunos escenarios y casos de uso que se beneficiarían de los Servicios de dominio de Azure Active Directory (AD).

## Administración sencilla y segura de máquinas virtuales de Azure
A medida que los servidores y otras infraestructuras alcanzan el final de su ciclo de vida, Contoso traslada a la nube muchas aplicaciones que actualmente se hospedan de forma local. Su norma actual de TI dictamina que los servidores que hospedan aplicaciones corporativas se deben unir a un dominio y administrarse mediante la directiva de grupo. El administrador de TI de Contoso prefiere unir a un dominio las máquinas virtuales implementadas en Azure, con el fin de facilitar la administración (es decir, los administradores pueden iniciar sesión con sus credenciales corporativas). Contoso preferiría no tener que implementar, supervisar y administrar los controladores de dominio de Azure a fin de proteger las máquinas virtuales de Azure.

Algunos puntos importantes que se deben tener en cuenta al considerar este escenario:

- Los dominios administrados que proporcionan los Servicios de dominio de Azure AD solo admiten una estructura de unidad organizativa (unidad organizativa) plana. Todas las máquinas unidas a un dominio residen en una única unidad organizativa plana y no se admiten estructuras jerárquicas de unidad organizativa.
- Los Servicios de dominio de Azure AD admiten una directiva de grupo simple en forma de un GPO integrado para cada uno de los contenedores de usuarios y equipos. No puede destinar la directiva de grupo por unidad organizativa o departamento, realizar el filtrado de WMI ni crear GPO personalizados.
- Los Servicios de dominio de Azure AD son compatible con el esquema base de objeto de equipo de AD. No puede extender el esquema del objeto de equipo.


## Subida y desplazamiento de una aplicación local que usa la autenticación de enlace LDAP a los Servicios de infraestructura de Azure
Contoso cuenta con una aplicación local que se compró a un ISV hace muchos años. La aplicación está actualmente en modo de mantenimiento por el ISV y para Contoso resulta excesivamente caro solicitar cambios en la aplicación. Esta aplicación tiene un front-end basado en web que recopila las credenciales de usuario mediante un formulario web y luego autentica a los usuarios mediante la realización de un enlace LDAP a Active Directory corporativo. A Contoso le gustaría migrar esta aplicación a los Servicios de infraestructura de Azure. Es conveniente que la aplicación funcione como está, sin que sea necesario realizar ningún cambio. Además, los usuarios deben poder autenticarse con las credenciales corporativas existentes y sin tener que volver a entrenar a los usuarios a hacer las cosas de manera diferente. En otras palabras, los usuarios finales deben olvidarse de dónde se ejecuta la aplicación y la migración debe ser transparente para ellos.

Algunos puntos importantes que se deben tener en cuenta al considerar este escenario:

- Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura de LDAP a dominios administrados proporcionado por los Servicios de dominio de Azure AD.
- Los usuarios no pueden cambiar su contraseña directamente en el dominio administrado. Los usuarios pueden cambiar su contraseña bien mediante el mecanismo de autoservicio de cambio de contraseña de Azure AD o en el directorio local. Estos cambios se sincronizarán y están disponibles automáticamente en el dominio administrado.


## Subida y desplazamiento de una aplicación local que usa la lectura LDAP para acceder al directorio a los Servicios de infraestructura de Azure
Contoso tiene una aplicación de línea de negocio local que se desarrolló hace casi una década. Esta aplicación tiene en cuenta el directorio y se diseñó para funcionar con Windows Server AD. La aplicación usa LDAP (Lightweight Directory Access Protocol) para leer los atributos o información sobre los usuarios de Active Directory. La aplicación no modifica los atributos ni escribe en el directorio. A Contoso le gustaría migrar esta aplicación a los Servicios de infraestructura de Azure y retirar el hardware local antiguo que actualmente hospeda esta aplicación. La aplicación no se puede volver a escribir para que use las modernas API de directorio, como la API de Azure AD Graph basada en REST. Por lo tanto, se necesita una opción de subida y desplazamiento mediante la cual se pueda migrar la aplicación para ejecutarse en la nube, sin modificar el código o volver a escribirla.

Algunos puntos importantes que se deben tener en cuenta al considerar este escenario:

- Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura de LDAP a dominios administrados proporcionado por los Servicios de dominio de Azure AD.
- Asegúrese de que la aplicación no necesita un esquema de Active Directory ampliado o personalizado. No se admiten extensiones de esquema en los Servicios de dominio de Azure AD.
- Asegúrese de que la aplicación no necesita acceso a LDAPS. LDAPS no es compatible con los Servicios de dominio de Azure AD.


## Migración de una aplicación de dominio o servicio local a los Servicios de infraestructura de Azure
Contoso tiene una aplicación de almacén de software personalizada que incluye un front-end web, un servidor SQL y un servidor FTP de back-end. Aprovechan la autenticación integrada de Windows mediante cuentas de servicio para autenticar el web front-end en el servidor FTP. Les gustaría mover esta aplicación a los Servicios de infraestructura de Azure y preferirían no tener que implementar una máquina virtual de controlador de dominio en la nube para satisfacer las necesidades de identidad de esta aplicación. El administrador de TI de Contoso puede implementar los servidores que hospedan el front-end web, el servidor SQL y el servidor FTP en máquinas virtuales de Azure y unirlos a un dominio de los Servicios de dominio de Azure AD. En consecuencia, pueden usar la misma cuenta de servicio en su directorio para la autenticación de la aplicación.

Algunos puntos importantes que se deben tener en cuenta al considerar este escenario:

- Asegúrese de que la aplicación usa nombre de usuario y contraseña para la autenticación. Los Servicios de dominio de Azure AD no admiten la autenticación basada en certificado o tarjeta inteligente.


## Azure RemoteApp
Azure RemoteApp permite al administrador de Contoso crear una colección unida a un dominio. De esta forma, las aplicaciones remotas atendidas por Azure RemoteApp pueden ejecutarse en equipos unidos a un dominio y tener acceso a otros recursos con la autenticación integrada de Windows. Contoso puede usar los Servicios de dominio de Azure AD para proporcionar un dominio administrado que se emplea en las colecciones unidas a un dominio de Azure RemoteApp.

Para más información sobre este escenario de implementación, consulte el artículo del blog de servicios de escritorio remoto titulado [Lift-and-shift your workloads with Azure RemoteApp and Azure AD Domain Services](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx) (Elevación y desplazamiento de las cargas de trabajo con Azure RemoteApp y los Servicios de dominio de Azure AD).

<!---HONumber=AcomDC_0128_2016-->
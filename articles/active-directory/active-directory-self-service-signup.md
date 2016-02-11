<properties
	pageTitle="¿Qué es la suscripción de autoservicio de Azure? | Microsoft Azure"
	description="Información general de la suscripción de autoservicio de Azure: cómo administrar el proceso de suscripción."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/05/2016"
	ms.author="stevenpo"/>


# ¿Qué es la suscripción de autoservicio de Azure?

En este tema se explica el proceso de suscripción de autoservicio (conocido también como suscripción viral) y cómo adquirir un nombre de dominio DNS.

## Razones para usar la suscripción de autoservicio

- Permita que los usuarios tengan acceso a los servicios que desean más rápido.
- Cree ofertas (virales) basadas en correo electrónico para un servicio.
- Cree flujos de suscripción basados en correo electrónico que permitan a los usuarios crear identidades rápidamente con sus alias de correo electrónico del trabajo fáciles de recordar.
- Los inquilinos de Azure no administrados pueden crecer y convertirse en inquilinos administrados más adelante y reutilizarse para otros servicios.

## Términos y definiciones

+ **Suscripción de autoservicio**: método por el que un usuario se suscribe a un servicio en la nube y por el que se le crea automáticamente una identidad en Azure Active Directory (AD) basado en su dominio de correo electrónico.
+ **Inquilino de Azure no administrado**: directorio donde se crea la identidad. Un inquilino no administrado es un directorio que no tiene administrador global.
+ **Usuario comprobado por correo electrónico**: tipo de cuenta de usuario en Azure AD. Un usuario que tiene una identidad que se crean automáticamente después de suscribirse a una oferta de autoservicio se conoce como usuario comprobado por correo electrónico. Un usuario comprobado por correo electrónico es un miembro regular de un directorio etiquetado con creationmethod = EmailVerified.

## Experiencia del usuario

Por ejemplo, supongamos que un usuario cuyo correo electrónico es Dan@BellowsCollege.com recibe archivos confidenciales por correo electrónico. Los archivos se han protegido con Azure Rights Management (Azure RMS). Pero la organización de Dan, Bellows College, no se ha suscrito a Azure RMS ni ha implementado Active Directory RMS. En este caso, Dan puede suscribirse a una suscripción gratuita de RMS para usuarios a fin de leer los archivos protegidos.

Si Dan es el primer usuario con una dirección de correo electrónico de BellowsCollege.com en suscribirse a esta oferta de autoservicio, se creará un inquilino no administrado para BellowsCollege.com en Azure AD. Si otros usuarios del dominio BellowsCollege.com se suscriben a esta oferta o a una oferta de autoservicio similar, también se crearán para ellos cuentas de usuario comprobado por correo electrónico en el mismo inquilino no administrado en Azure.

## Experiencia del administrador

Un administrador que posee el nombre de dominio DNS de un inquilino de Azure no administrado puede adquirir o fusionar el inquilino después de demostrar la propiedad. En las secciones siguientes se explica con más detalle la experiencia del administrador, pero a continuación se incluye un resumen:

- Al adquirir un inquilino de Azure no administrado, se convierte en administrador global del inquilino no administrado. Esto se denomina a veces adquisición interna.
- Al fusionar un inquilino de Azure no administrado, agregue el nombre de dominio DNS del inquilino no administrado a su inquilino de Azure administrado. Se creará una asignación de usuarios a recursos para que los usuarios puedan seguir teniendo acceso a los servicios sin interrupción. Esto se denomina a veces adquisición externa.

## ¿Qué se crea en Azure Active Directory?

#### Inquilino

- Se crea un inquilino de Azure Active Directory para el dominio, un inquilino por dominio.
- El directorio del inquilino de Azure AD no tiene administrador global.

#### Usuarios

- Para cada usuario que se suscribe , se crea un objeto en el inquilino de Azure AD.
- Cada objeto de usuario se marca como viral.
- Cada usuario tiene acceso al servicio al que se ha suscrito.

### ¿Cómo puedo reclamar un inquilino de Azure AD de autoservicio para un dominio de mi propiedad?

Puede reclamar un inquilino de Azure AD de autoservicio realizando la validación del dominio. La validación del dominio demuestra que es el propietario del dominio mediante la creación de registros DNS.

Hay dos formas de realizar una adquisición de DNS de un inquilino de Azure AD:

- adquisición interna (el administrador detecta a un inquilino de Azure no administrado y desea convertirlo en un inquilino administrado)
- adquisición externa (el administrador intenta agregar un nuevo dominio a su inquilino de Azure administrado).

Pude que le interese validar un dominio que posee porque va a adquirir un inquilino no administrado después de que un usuario realice una suscripción de autoservicio, o bien puede que quiera agregar un dominio nuevo a un inquilino administrado existente. Por ejemplo, tiene un dominio denominado contoso.com y desea agregar un nuevo dominio denominado contoso.co.uk o contoso.uk.

## ¿Qué es la adquisición de un dominio?  

En esta sección se describe cómo validar la propiedad de su dominio.

### ¿Qué es la validación de dominios y por qué se usa?

Para realizar operaciones en un inquilino, Azure AD necesita que valide la propiedad del dominio DNS. La validación del dominio permite reclamar el inquilino y ascender el inquilino de autoservicio a un inquilino administrado o fusionar el inquilino de autoservicio en un inquilino administrado existente.

## Ejemplos de validación de dominio

Hay dos formas de realizar una adquisición de DNS de un inquilino:

+ adquisición interna (por ejemplo, un administrador detecta un inquilino no administrado de autoservicio y desea convertirlo en inquilino administrado)
+ adquisición externa (por ejemplo, el administrador intenta agregar un nuevo dominio a un inquilino administrado).

### Adquisición interna: ascienda un inquilino no administrado de autoservicio a un inquilino administrado

Cuando realiza una adquisición interna, el inquilino pasa de ser un inquilino no administrado a un inquilino administrado. Debe completar la validación del nombre de dominio DNS, donde crea un registro MX o un registro TXT en la zona DNS. Esa acción:

+ Valida la propiedad de su dominio
+ Hace que el inquilino sea administrado
+ Le convierte en administrador global del inquilino

Supongamos que un administrador de TI de Bellows College detecta que los usuarios de la escuela se han suscrito a ofertas de autoservicio. Como propietario registrado del nombre DNS BellowsCollege.com, el administrador de TI puede validar la propiedad del nombre DNS en Azure y adquirir el inquilino no administrado. A continuación, el inquilino se convierte en un inquilino administrado y al administrador de TI se le asigna el rol de administrador global para el directorio BellowsCollege.com.

### Adquisición externa: fusione un inquilino de autoservicio en un inquilino administrado existente

En una adquisición externa, ya tiene un inquilino administrado y desea que todos los usuarios y grupos de un inquilino no administrado se unan a ese inquilino administrado en lugar de poseer dos inquilinos independientes.

Como administrador de un inquilino administrado, se agrega un dominio que tiene asociado un inquilino no administrado.

Por ejemplo, supongamos que es administrador de TI y ya tiene un inquilino administrado para Contoso.com, un nombre de dominio registrado para su organización. Descubre que los usuarios de su organización han realizado una suscripción de autoservicio a una oferta con un nombre de dominio de correo electrónico user@contoso.co.uk, que es otro nombre de dominio que posee su organización. Actualmente, esos usuarios tienen cuentas en un inquilino no administrado para contoso.co.uk.

Usted no desea administrar dos inquilinos independientes, por lo que fusiona el inquilino no administrado para contoso.co.uk en su inquilino administrado de TI existente para contoso.com.

La adquisición externa sigue el mismo proceso de validación de DNS que la adquisición interna. La diferencia es que se reasignan los usuarios y servicios al inquilino administrado de TI.

#### ¿Cuál es el impacto de realizar una adquisición externa?

Con una adquisición externa, se crea una asignación de usuarios a recursos para que los usuarios sigan teniendo acceso a los servicios sin interrupción. Muchas aplicaciones, como RMS para usuarios, administran la asignación de usuarios a los recursos bien y los usuarios pueden seguir teniendo acceso a los servicios sin cambios. Si una aplicación no administra la asignación de usuarios a recursos de forma eficaz, es posible que se bloquee la adquisición externa explícitamente para evitar una experiencia deficiente de los usuarios.

#### Admisión de la adquisición del inquilino por servicio

Actualmente, los servicios siguientes admiten la adquisición:

- RMS


Los siguientes servicios admitirán la adquisición próximamente:

- PowerBI

Los siguientes servicios no la admiten y requieren una acción adicional del administrador para migrar datos de usuario después de una adquisición externa.

- SharePoint/OneDrive


## Realización de una adquisición de nombre de dominio DNS

Tiene unas cuantas opciones para realizar una validación de dominio (y realizar una adquisición si lo desea):

1.  Portal de administración de Azure

	Una adquisición se desencadena agregando un dominio. Si ya existe un inquilino para el dominio, tendrá la opción de realizar una adquisición externa.

	Inicio de sesión en el portal de Azure con credenciales Vaya al inquilino existente y a **Agregar dominio**.

2.  Office 365

	Puede usar las opciones de la [Administrar dominios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) en Office 365 para trabajar con sus dominios y registros DNS. Consulte [Comprobar el dominio en Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

	Los pasos siguientes son necesarios para realizar una validación con Windows PowerShell.

	Paso |	Cmdlet que se debe usar
	-------	| -------------
	Crear un objeto de credencial | Get-Credential
	Conectarse a Azure | Connect-MsolService
	Obtener una lista de dominios | Get-MsolDomain
	Crear un desafío | Get-MsolDomainVerificationDns
	Crear un registro DNS | Realice esta acción en su servidor de DNS
	Comprobar el desafío | Confirm-MsolEmailVerifiedDomain

Por ejemplo:

1. Conéctese a Azure AD con las credenciales que se usaron para responder a la oferta de autoservicio: import-module MSOnline $msolcred = get-credential connect-msolservice -credential $msolcred

2. Obtenga una lista de dominios:

	Get-MsolDomain

3. A continuación, ejecute el cmdlet Get-MsolDomainVerificationDns para crear un desafío:

	Get-MsolDomainVerificationDns –DomainName *nombre\_de\_dominio* –Mode DnsTxtRecord

	Por ejemplo:

	Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord

4. Copie el valor (el desafío) que se devuelve desde este comando.

	Por ejemplo:

	MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. En el espacio de nombres DNS público, cree un registro txt de DNS que contenga el valor que copió en el paso anterior.

	El nombre de este registro es el nombre del dominio principal, por lo que si crea este registro de recursos con el rol DNS desde Windows Server, deje el nombre del registro en blanco y pegue el valor en el cuadro de texto.

6. Ejecute el cmdlet Confirm-MsolDomain para comprobar el desafío:

	Confirm-MsolEmailVerifiedDomain -DomainName *nombre\_de\_dominio*

	Por ejemplo:

	Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Un desafío correcto le devuelve el mensaje sin errores.

## ¿Cómo controlo la configuración de autoservicio?

Actualmente, los administradores tienen dos controles de autoservicio . Pueden controlar:

- Si los usuarios pueden unirse o no al inquilino a través de correo electrónico.
- Si los usuarios pueden concederse o no licencias para aplicaciones y servicios.


### ¿Cómo puedo controlar estas capacidades?

Un administrador puede configurar estas capacidades con estos parámetros Set-MsolCompanySettings de cmdlet de Azure AD:

+ **AllowEmailVerifiedUsers** controla si un usuario puede crear o unirse a un inquilino no administrado. Si establece este parámetro en $false, ningún usuario comprobado por correo electrónico se puede unir al inquilino.
+ **AllowAdHocSubscriptions** controla la capacidad de los usuarios de realizar suscripciones de autoservicio. Si establece el parámetro en $false, ningún usuario puede realizar suscripciones de autoservicio.


### ¿Cómo funciona los controles conjuntamente?

Estos dos parámetros se pueden usar juntos para definir un control más preciso de la suscripción de autoservicio. Por ejemplo, el comando siguiente permitirá a los usuarios realizar suscripciones de autoservicio pero solo si estos usuarios ya tienen una cuenta en Azure AD (en otras palabras, los usuarios que necesitan crear una cuenta comprobada por correo electrónico no pueden realizar suscripciones de autoservicio):

	Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

En el siguiente diagrama se explican las distintas combinaciones de estos parámetros y las condiciones resultantes para el inquilino y la suscripción de autoservicio.

![][1]

Para obtener más información y ejemplos de cómo usar estos parámetros, consulte [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## Otras referencias

-  [Instalación y configuración de Azure PowerShell](../powershell-install-configure/)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

<!---HONumber=AcomDC_0107_2016-->
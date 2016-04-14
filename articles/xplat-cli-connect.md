<properties
	pageTitle="Inicio de sesión en Azure desde la CLI | Microsoft Azure"
	description="Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure) para Mac, Linux y Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)

La CLI de Azure es un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. En este artículo se describe cómo conectarse a su suscripción de Azure desde la CLI de Azure para utilizar todos los comandos de la CLI. Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](xplat-cli-install.md).



Hay dos maneras de conectarse a su suscripción desde la CLI de Azure:

* **Iniciar sesión en Azure con una cuenta profesional o educativa o una identidad de cuenta Microsoft**: esto utiliza cualquier tipo de identidad de cuenta para autenticar. La CLI actual también admite la autenticación interactiva para las cuentas que tienen habilitada la autenticación multifactor. Después de iniciar sesión interactivamente, puede utilizar el Administrador de recursos o los comandos clásicos (administración de servicios).

* **Descarga y uso de un archivo de configuración de publicación**: de este modo se instala un certificado en el equipo local que le permite realizar tareas de administración durante el período de validez de la suscripción y el certificado. Este método solo permite usar comandos clásicos (administración de servicios).

Para obtener más información acerca de la administración de la autenticación y la suscripción, consulte ["¿Cuál es la diferencia entre la autenticación basada en cuentas y la basada en certificados?"][authandsub].

En caso de no tener ninguna cuenta de Azure, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][free-trial].

>[AZURE.NOTE] Si está utilizando una versión de la CLI de Azure anterior a 0.9.10, puede utilizar el comando `azure login` solo con las identidades de cuenta profesional o educativa; las identidades de la cuenta Microsoft no funcionan. Sin embargo, puede utilizar cualquier identidad para iniciar sesión en su cuenta con el comando `azure login` interactivo con versiones de la CLI de Azure 0.9.10 y posteriores.
>
Las versiones 0.9.9. y superiores son compatibles con la autenticación multifactor.



## Uso del método de inicio de sesión interactivo

Utilice el comando `azure login` --sin argumentos-- para autenticarse interactivamente con cualquiera de estas opciones:

- una cuenta profesional o educativa que requiere autenticación multifactor, o
- una identidad de cuenta Microsoft cuando desea tener acceso a la funcionalidad del modo de implementación del Administrador de recursos

> [AZURE.NOTE]  En ambos casos, la autenticación y la autorización se realizan con Azure Active Directory. Si usa una identidad de cuenta de Microsoft, el proceso de inicio de sesión obtiene acceso a su dominio predeterminado de Azure Active Directory. (Si se registró para obtener una prueba gratuita, puede que no sepa que Azure Active Directory creó un dominio predeterminado para su cuenta).

Iniciar sesión de forma interactiva es fácil; escriba `azure login` y siga las instrucciones tal como se muestra a continuación:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Copie el código que se muestra más arriba y abra http://aka.ms/devicelogin en un explorador. Indique el código y se le pedirá que escriba el nombre de usuario y la contraseña para la identidad que desea utilizar. Cuando se complete ese proceso, el shell de comandos completará el registro en curso. Sería algo parecido a lo siguiente:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Uso del inicio de sesión no interactivo con una cuenta profesional o educativa


El método de inicio de sesión no interactivo solo funciona con una cuenta profesional o educativa, también conocida como *cuenta de la organización*. Esta cuenta está administrada por su organización y definida en el Azure Active Directory de la organización. También puede [crear una cuenta de la organización](#create-an-organizational-account) si no tiene una, o bien puede [crear un identificador profesional o educativo a partir del identificador de su cuenta Microsoft](./virtual-machines/resource-group-create-work-id-from-personal.md). Esto requiere que especifique un nombre de usuario o un nombre de usuario y una contraseña para el comando `azure login`, de este modo:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Escriba la contraseña cuando se le solicite.

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **Para cerrar sesión**, utilice el comando siguiente:

		azure logout -u <username>

	Si las suscripciones asociadas a la cuenta solo se autenticaron con Active Directory, al cerrar sesión se elimina la información de la suscripción del perfil local. Sin embargo, si también se ha importado un archivo de configuración de publicación para las suscripciones, al cerrar sesión se elimina solo la información relacionada con Active Directory del perfil local.

## Uso del método del archivo de configuración de publicación

Si solo necesita usar los comandos clásicos de la CLI (administración de servicios), puede conectarse utilizando un archivo de configuración de publicación.

* **Para descargar el archivo de configuración de publicación** para su cuenta, use el comando siguiente:

		azure account download

Esto abrirá el explorador predeterminado y le solicitará iniciar sesión en el [Portal de Azure clásico][portal]. Después de iniciar sesión, se descarga un archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

> [AZURE.NOTE] Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.
>
> Una vez seleccionado usando la página de descarga, o visitando el Portal de Azure clásico, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal clásico como la página de descarga. Cuando se haya establecido el predeterminado, verá el texto "__click here to return to the selection page__" en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

* **Para importar el archivo de configuración de publicación**, ejecute el comando siguiente:

		azure account import <path to your .publishsettings file>

	Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings`, porque ya no lo necesita la CLI de Azure y supone un riesgo para la seguridad, ya que se puede usar para obtener acceso a su suscripción.


## Varias suscripciones

Si tiene varias suscripciones de Azure, la conexión a Azure le dará acceso a todas las suscripciones asociadas a sus credenciales. Se selecciona una suscripción como predeterminada y la CLI de Azure la utilizará al realizar operaciones. Podrá ver las suscripciones, así como cuál es la predeterminada, mediante el comando `azure account list`. Este comando devuelve información similar a la siguiente:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

En la lista anterior, la columna **Current** indica las suscripciones predeterminadas actuales como Azure-sub-1. Para cambiar la suscripción predeterminada, utilice el comando `azure account set` y especifique qué suscripción quiere que sea la predeterminada. Por ejemplo:

	azure account set Azure-sub-2

Esto cambia la suscripción predeterminada a Azure-sub-2.

> [AZURE.NOTE] El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; los nuevos comandos de la CLI de Azure, ya los ejecute desde la misma instancia de línea de comandos o desde una instancia diferente, usan la nueva suscripción predeterminada.

Si desea usar una suscripción no predeterminada con la CLI de Azure, pero no quiere cambiar la predeterminada actualmente, puede usar la opción `--subscription` para el comando y proporcionar el nombre de la suscripción que desee usar para la operación.

Una vez que esté conectado a su suscripción de Azure, puede empezar a usar los comandos de la CLI de Azure.

## Almacenamiento de la configuración de la CLI

Independientemente de que inicie sesión con una cuenta profesional o educativa o importe la configuración de publicación, el perfil de CLI y los registros se almacenan en un directorio `.azure` ubicado en su directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:

* En Windows, modifique las propiedades del directorio o use BitLocker.
* En Mac, active FileVault para el directorio.
* En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características similares.

## Recursos adicionales

* [Uso de la interfaz de la línea de comandos de Azure con los comandos de la Administración de servicios (modo clásico)][cliasm]

* [Uso de la interfaz de la línea de comandos de Azure con el Administrador de recursos][cliarm]

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0204_2016-->
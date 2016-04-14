<properties
	pageTitle="¿Qué es el Almacén de claves de Azure? | Microsoft Azure"
	description="El Almacén de claves de Azure ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Almacén de claves de Azure, los clientes pueden cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través de claves que están protegidas por módulos de seguridad de hardware (HSM)."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/08/2016"
	ms.author="cabailey"/>



# ¿Qué es el Almacén de claves de Azure?

Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](../../../../pricing/details/key-vault/).

## Introducción

El Almacén de claves de Azure ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Almacén de claves, puede cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través del uso de claves que están protegidas por módulos de seguridad de hardware (HSM). Para tener mayor seguridad, puede importar o generar las claves en HSM. Si decide hacerlo, Microsoft procesará las claves en los HSM validados de FIPS 140-2 nivel 2 (hardware y firmware).

Almacén de claves agiliza el proceso de administración de claves y le permite mantener el control de claves que obtienen acceso a sus datos y los cifran. Los desarrolladores pueden crear claves para desarrollo y prueba en minutos y, a continuación, migrarlas sin problemas a claves de producción. Los administradores de seguridad pueden conceder (y revocar) permisos a las claves según sea necesario.

Utilice la tabla siguiente para comprender mejor cómo Almacén de claves puede ayudar a satisfacer las necesidades de los programadores y de los administradores de seguridad.





| Rol | Declaración del problema | Resuelto por Almacén de claves de Azure |
| ------------- |-------------|-----|
| Desarrollador para una aplicación de Azure | “Quiero escribir una aplicación para Azure que use claves para firma y cifrado, pero quiero que sean externas desde mi aplicación para que la solución sea adecuada para una aplicación que se distribuya geográficamente. <br/><br/>También quiero que estas claves y secretos estén protegidos, sin tener que escribir el código, y quiero que me resulten fáciles de usar desde mi aplicación, con el máximo rendimiento". | √ Las claves se almacenan en un almacén y las invoca un URI cuando es necesario.<br/><br/> √ Las claves se protegen mediante Azure usando algoritmos estándar de la industria, longitudes de clave y módulos de seguridad de hardware (HSM).<br/><br/> √ Las claves se procesan en HSM que residen en los mismos centros de datos de Azure que las aplicaciones, que proporcionan una mayor confiabilidad y una menor latencia que si las claves residieran en una ubicación separada, como en local.|
| Desarrollador para software como servicio (SaaS): |“No quiero asumir la responsabilidad, ni tampoco la posible responsabilidad, de las claves y los secretos de inquilino de mis clientes. <br/><br/>Quiero que los clientes posean y administren sus claves de modo que pueda concentrarse en hacer lo que hago mejor, que es proporcionar las características de software principales.” | √ Los clientes pueden importar sus propias claves a Azure y administrarlas. Cuando se necesita una aplicación SaaS para realizar operaciones criptográficas usando claves de sus clientes, el Almacén de claves lo hace en nombre de la aplicación. La aplicación no ve las claves de los clientes.|
| Responsable principal de la seguridad (CSO) | “Quiero saber que nuestras aplicaciones cumplen con HSM FIPS 140-2 de nivel 2 para administración de claves segura. <br/><br/>Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de las mismas. <br/><br/>Y aunque usamos varios servicios y recursos de Azure, quiero administrar las claves desde una ubicación única en Azure.” |√ Los HSM tienen la validación FIPS 140-2 de nivel 2.<br/><br/>√ El Almacén de claves está diseñado para que Microsoft no pueda ver ni extraer las claves.<br/><br/>√ Registro prácticamente en tiempo real del uso de las claves.<br/><br/>√ El almacén proporciona una sola interfaz, independientemente del número de almacenes que tenga en Azure, las regiones que admitan y las aplicaciones que los usen. |


Cualquier persona que tenga una suscripción de Azure puede crear y usar almacenes de claves. Aunque el Almacén de claves beneficia a los desarrolladores y los administradores de seguridad, el administrador de una organización que administra otros servicios de Azure, podría implementarlo y administrarlo. Por ejemplo, este administrador iniciaría sesión con una suscripción de Azure, crearía un almacén para la organización en el que almacenar las claves y, a continuación, asumiría la responsabilidad de las tareas operativas, como:

+ Crear o importar una clave o un secreto
+ Revocar o eliminar una clave o un secreto
+ Autorizar usuarios o aplicaciones para acceder al almacén de claves para que puedan administrar o usar sus claves y secretos
+ Configurar el uso de claves (por ejemplo, para firmar o cifrar)
+ Supervisar el uso de claves

Este administrador podría ofrecer después a los desarrolladores los URI para llamar desde sus aplicaciones y proporcionar a su administrador de seguridad la información de registro de uso de claves.

   ![Información general del Almacén de claves de Azure][1]

Los desarrolladores también pueden administrar las claves directamente mediante API. Para más información, consulte la [guía para desarrolladores del Almacén de claves](key-vault-developers-guide.md).

## Pasos siguientes

Para consultar un tutorial de introducción para que un administrador, vea [Introducción al Almacén de claves de Azure](key-vault-get-started.md).

Para obtener más información acerca del registro de uso para el Almacén de claves, consulte [Registro del Almacén de claves de Azure](key-vault-logging.md).

Para obtener más información acerca del uso de claves y secretos con el Almacén de claves de Azure, vea [Acerca de claves y secretos](https://msdn.microsoft.com/library/azure/dn903623.aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png

<!---HONumber=AcomDC_0114_2016-->
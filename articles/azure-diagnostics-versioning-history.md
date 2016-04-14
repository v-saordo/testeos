<properties
	pageTitle="Historial de versiones de Diagnósticos de Azure"
	description="Explicación de los cambios en las distintas versiones de Diagnósticos de Azure incluidas en diferentes versiones de los SDK de Microsoft Azure."
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="robb"/>


# Historial de versiones de Diagnósticos de Microsoft Azure

¿Acaba de llegar a Diagnósticos de Azure? Consulte la [información general sobre Diagnósticos de Azure](azure-diagnostics.md).

Normalmente, cada versión del SDK de Azure se distribuye con una nueva versión de Diagnósticos de Azure. En la siguiente tabla se describen las versiones del SDK de Azure y de Diagnósticos de Azure asociadas a la versión del SDK.



Versión del SDK de Azure | Versión de Diagnósticos de Azure | Modelo
--- | --- | ---
1\.x | 1\.0 | complemento
2\.0 a 2.4| 1\.0 | "
2\.5 | 1\.2 | extensión
2\.6 | 1\.3 | "
2\.7 | 1\.4 | "
2\.8 | 1\.5 | "


La versión más reciente es la 1.5, que se incluye con el SDK de Azure 2.8. La versión de la extensión de Diagnósticos de Azure que se incluye con el SDK solo se utiliza para escenarios locales del emulador. Cualquier aplicación implementada utiliza automáticamente la versión más reciente cuando se ejecuta en Azure, independientemente de la versión del SDK con que se haya generado la aplicación. Sin embargo, a menos que instale el último SDK de Azure, puede que no tenga todas las herramientas que le pueden ayudar a utilizar las nuevas funcionalidades.

A continuación se describen varias funcionalidades y varios cambios.

## Azure SDK 2.8
Azure SDK 2.8 agregó la capacidad de enviar datos de diagnóstico a [Application Insights](./application-insights/app-insights-cloudservices.md), lo que permitió diagnosticar los problemas tanto en aplicación como en la infraestructura y el sistema.

## Cambios de diagnóstico de Azure 2.6

Para los proyectos de Servicio en la nube de Azure SDK 2.6 en Visual Studio, se realizaron los siguientes cambios. (Estos cambios también se aplican a las versiones posteriores del SDK de Azure.)

- El emulador local ahora es compatible con diagnósticos. Esto significa que puede recopilar datos de diagnóstico y asegurarse de que su aplicación crea los seguimientos correctos mientras desarrolla y realiza pruebas en Visual Studio. La cadena de conexión `UseDevelopmentStorage=true` habilita la colección de datos de diagnóstico mientras ejecuta el proyecto de servicio en la nube en Visual Studio mediante el emulador de almacenamiento de Azure. Todos los datos de diagnóstico se recopilan en la cuenta de almacenamiento (Almacenamiento de desarrollo).

- La cadena de conexión de la cuenta de almacenamiento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) se almacena una vez más en el archivo de configuración de servicio (.cscfg). En Azure SDK 2.5 se especificó la cuenta de almacenamiento de diagnóstico en el archivo diagnostics.wadcfgx.

Existen algunas diferencias importantes entre el funcionamiento de la cadena de conexión en Azure SDK 2.4 y Azure SDK 2.6 y versiones posteriores.

- En Azure SDK 2.4 y versiones anteriores, la cadena de conexión se usaba como tiempo de ejecución mediante el complemento de diagnóstico para obtener la información de la cuenta de almacenamiento para la transferencia de registros de diagnóstico.

- En Azure SDK 2.6 y versiones posteriores, Visual Studio usa la cadena de conexión de diagnóstico para configurar la extensión de diagnóstico con la información de la cuenta de almacenamiento adecuada durante la publicación. La cadena de conexión le permite definir las cuentas de almacenamiento diferentes para diferentes configuraciones de servicio que Visual Studio usará al publicar. Sin embargo, porque ya no está disponible el complemento de diagnóstico (después de Azure SDK 2.5), el archivo .cscfg por sí solo no puede habilitar la extensión de diagnóstico. Debe habilitar la extensión por separado a través de herramientas como Visual Studio o PowerShell.

- Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, la salida del paquete de Visual Studio también contiene el XML de configuración pública para la extensión de diagnósticos para cada rol. Visual Studio usa la cadena de conexión de diagnósticos para rellenar la información de la cuenta de almacenamiento presente en la configuración pública. Los archivos de configuración públicas se crean en la carpeta Extensiones y siguen el patrón PaaSDiagnostics.<RoleName>.PubConfig.xml. Cualquier implementación basada en PowerShell puede usar este patrón para asignar cada configuración a un rol.

- El portal de Azure también usa la cadena de conexión del archivo .cscfg para obtener acceso a los datos de diagnóstico, por lo que puede aparecer en la pestaña **Supervisión**. La cadena de conexión es necesaria para configurar el servicio para que muestre los datos de supervisión detallada en el portal.

### Migración de proyectos a Azure SDK 2.6 y versiones posteriores

Al migrar de Azure SDK 2.5 a Azure SDK 2.6 o versiones posteriores, si tenía una cuenta de almacenamiento de diagnósticos especificada en el archivo .wadcfgx, permanecerá allí. Para aprovechar la flexibilidad de utilizar cuentas de almacenamiento diferentes para distintas configuraciones de almacenamiento, tendrá que agregar manualmente la cadena de conexión al proyecto. Si está migrando un proyecto de Azure SDK 2.4 o una versión anterior a Azure SDK 2.6, se conservarán las cadenas de conexión de diagnóstico. Sin embargo, tenga en cuenta los cambios en cómo se tratan las cadenas de conexión en Azure SDK 2.6 tal como se especifica en la sección anterior.

### Cómo determina Visual Studio la cuenta de almacenamiento de diagnósticos

- Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio lo usa para configurar la extensión de diagnóstico al publicar y al generar los archivos xml de configuración pública durante el empaquetado.

- Si no se especifica ninguna cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio recurre a usar la cuenta de almacenamiento especificada en el archivo .wadcfgx para configurar la extensión de diagnóstico al publicar, y a generar los archivos xml de configuración pública durante el empaquetado.

- La cadena de conexión de diagnóstico del archivo .cscfg tiene prioridad sobre la cuenta de almacenamiento del archivo .wadcfgx. Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio la usa y omite la cuenta de almacenamiento en .wadcfgx.

### ¿Qué hace la casilla "Actualizar las cadenas de conexión de almacenamiento de desarrollo..."?

La casilla **Actualizar las cadenas de conexión de almacenamiento de desarrollo para el diagnóstico y el almacenamiento en caché con las credenciales de la cuenta de almacenamiento de Microsoft Azure al publicar en Microsoft Azure** le ofrece una manera cómoda de actualizar cualquier cadena de conexión de cuenta de almacenamiento de desarrollo con la cuenta de almacenamiento de Azure especificada durante la publicación.

Por ejemplo, supongamos que activa esta casilla y la cadena de conexión de diagnóstico especifica `UseDevelopmentStorage=true`. Al publicar el proyecto en Azure, Visual Studio actualizará automáticamente la cadena de conexión de diagnóstico con la cuenta de almacenamiento que especificó en el Asistente para publicación. Sin embargo, si se especificó una cuenta de almacenamiento real como la cadena de conexión de diagnóstico, se usará dicha cuenta en su lugar.

## Diferencias de funcionalidad de diagnóstico entre Azure SDK 2.4 y versiones anteriores y Azure SDK 2.5 y versiones posteriores

Si está actualizando su proyecto de Azure SDK 2.4 a Azure SDK 2.5 o versiones posteriores, debe tener en cuenta las siguientes diferencias de funcionalidad de diagnóstico.

- **Las API de configuración están desusadas**: la configuración mediante programación del diagnóstico está disponible en Azure SDK 2.4 o versiones anteriores, pero está en desuso en Azure SDK 2.5 y versiones posteriores. Si la configuración de diagnóstico está definida actualmente en el código, deberá volver a definir esa configuración desde el principio en el proyecto migrado para que el diagnóstico siga funcionando. El archivo de configuración de diagnóstico de Azure SDK 2.4 es diagnostics.wadcfg y diagnostics.wadcfgx, para Azure SDK 2.5 y versiones posteriores.

- **El diagnóstico para aplicaciones de servicio en la nube solo se puede configurar en el nivel de rol, no en el nivel de instancia.**

- **Cada vez que implementa la aplicación, se actualiza la configuración de diagnóstico**: esto puede provocar problemas de paridad si cambia la configuración de diagnóstico del Explorador de servidores y luego vuelve a implementar la aplicación.

- **En Azure SDK 2.5 y versiones posteriores, los volcados de memoria se configuran en el archivo de configuración de diagnóstico, no en el código**: si tiene volcados de memoria configurados en el código, tendrá que transferir manualmente la configuración del código al archivo de configuración, porque los volcados de memoria no se transfieren durante la migración a Azure SDK 2.6.

<!---HONumber=AcomDC_0302_2016-->
<properties
   pageTitle="Azure AD Connect Sync: Conector de PowerShell | Microsoft Azure"
   description="En este artículo se describe cómo configurar el conector de Windows PowerShell de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Referencia técnica del conector de Windows PowerShell

En este artículo se describe el conector de Windows PowerShell. El artículo se aplica a los siguientes productos:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Debe usar la revisión 4.1.3461.0 o posterior ([KB2870703](https://support.microsoft.com/kb/2870703)).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## Información general sobre el conector de PowerShell

El conector de PowerShell le permite integrar el servicio de sincronización con los sistemas externos que ofrecen interfaces de programación de aplicaciones (API) basadas en Windows PowerShell. El conector proporciona un puente entre las funcionalidades del marco del agente de administración de conectividad extensible 2 (ECMA2) basado en llamadas y Windows PowerShell. Para obtener más información sobre el marco ECMA, consulte [Extensible Connectivity 2.2 Management Agent Reference](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### Requisitos previos

Antes de usar el conector, asegúrese de que tiene lo siguiente en el servidor de sincronización, además de cualquier revisión mencionada antes:

- Microsoft .NET 4.5.2 Framework o posterior
- Windows PowerShell 2.0, 3.0 o 4.0

La directiva de ejecución en el servidor del servicio de sincronización debe configurarse para permitir que el conector ejecute scripts de Windows PowerShell. A menos que los scripts que va a ejecutar el conector estén firmados digitalmente, configure la directiva de ejecución mediante la ejecución de este comando:

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## Creación de un nuevo conector

Para crear un conector de Windows PowerShell en el servicio de sincronización, debe proporcionar una serie de scripts de Windows PowerShell que ejecuten los pasos solicitados por el servicio de sincronización. Dependiendo del origen de datos al que se va a conectar y de la funcionalidad que precise, variarán los scripts que debe implementar. En esta sección se describen los scripts que se pueden implementar, así como cuándo son necesarios.

El conector de Windows PowerShell está diseñado para almacenar cada uno de los scripts dentro de la base de datos del servicio de sincronización. Aunque es posible ejecutar scripts que se almacenan en el sistema de archivos, es mucho más fácil insertar el cuerpo de cada script directamente en la configuración del conector.

Para crear un conector de PowerShell, en **Servicio de sincronización**, seleccione **Agente de administración** y **Crear**. Seleccione el conector **PowerShell (Microsoft)**.

![Creación del conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### Conectividad

Después puede proporcionar los parámetros de configuración para conectarse a un sistema remoto. Estos parámetros los guardará con seguridad el servicio de sincronización y estarán disponibles de los scripts de Windows PowerShell cuando se ejecuta el conector.

![Conectividad](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Puede configurar los siguientes parámetros de conectividad:

**Conectividad**

| Parámetro | Valor predeterminado | Propósito |
| --- | --- | --- |
| Server | <Blank> | Nombre del servidor al que debe conectarse el conector. |
| Dominio | <Blank> | Dominio de la credencial para almacenar para su uso cuando se ejecuta el conector. |
| Usuario | <Blank> | Nombre de usuario de la credencial para almacenar para su uso cuando se ejecuta el conector. |
| Password | <Blank> | Contraseña de la credencial para almacenar para su uso cuando se ejecuta el conector. |
| Suplantación de la cuenta del conector | False | Cuando es true, el servicio de sincronización ejecutará los scripts de Windows PowerShell en el contexto de las credenciales suministradas anteriormente. Cuando sea posible, se recomienda que se use el parámetro $Credentials pasado a cada script en lugar de la suplantación. Para obtener más información sobre los permisos adicionales necesarios para usar este parámetro, consulte Configuración adicional para la suplantación. |
| Carga del perfil de usuario al suplantar | False | Indica a Windows que cargue el perfil de usuario de las credenciales del conector durante la suplantación. Si el usuario que se va a suplantar tiene un perfil móvil, el conector no cargará el perfil móvil. Para obtener más información sobre los permisos adicionales necesarios para usar este parámetro, consulte Configuración adicional para la suplantación. |
| Tipo de inicio de sesión al suplantar | None | Tipo de inicio de sesión durante la suplantación Para más información, consulte la documentación de [dwLogonType][dw]. |
| Solo scripts firmados | False | Si es true, el conector de Windows PowerShell valida que cada script tenga una firma digital válida. Si es false, asegúrese de que la directiva de ejecución de Windows PowerShell del servidor de servicio de sincronización es RemoteSigned o Unrestricted. |

**Módulo común**

El conector permite al administrador almacenar un módulo compartido de Windows PowerShell en la configuración. Cuando el conector ejecuta un script, el módulo de Windows PowerShell se extrae en el sistema de archivos, por lo que puede importarlo cada script.

Para los scripts de importación, exportación y sincronización de contraseña, el módulo común se extrae en la carpeta MAData del conector. Para los scripts de esquema, validación, jerarquía y detección de partición, el módulo común se extrae en la carpeta %TEMP%. En ambos casos, el script del módulo común extraído se denomina según la configuración de nombre de script del módulo común.

Para cargar un módulo denominado FIMPowerShellConnectorModule.psm1 desde la carpeta MAData, use la siguiente instrucción: `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para cargar un módulo denominado FIMPowerShellConnectorModule.psm1 desde la carpeta %TEMP%, use la siguiente instrucción: `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validación de parámetros**

El script de validación es un script de Windows PowerShell opcional que puede usarse para comprobar la validez de los parámetros de configuración del conector proporcionados por el administrador. La validación de las credenciales de conexión y del servidor, así como los parámetros de conectividad, son los usos comunes del script de validación. El script de validación se llama después de que se hayan modificado las pestañas y cuadros de diálogo siguientes:

- Conectividad
- Parámetros globales
- Configuración de la partición

El script de validación recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameterPage | [ConfigParameterPage][cpp] | La pestaña o cuadro de diálogo de configuración que desencadenó la solicitud de validación. |
| ConfigParameters | [KeyedCollection][keyk] [cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |

El script de validación debe devolver un solo objeto ParameterValidationResult a la canalización.

**Detección de esquema**

El script Detección de esquema es obligatorio. Este script devuelve los tipos de objeto y atributos, así como las restricciones de atributos que va a utilizar el servicio de sincronización al configurar las reglas de flujo de atributos. El script de detección de esquemas se ejecuta durante la creación del conector y rellenará el esquema del conector y después por la función de actualización del esquema del Administrador del servicio de sincronización.

El script de detección del esquema recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk] [cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |

El script debe devolver un único objeto [Schema][schema] a la canalización. El objeto Schema consta de objetos [SchemaType][schemaT] que representan los tipos de objeto (por ejemplo, usuarios o grupos). El objeto SchemaType contiene una colección de objetos [SchemaAttribute][schemaA] que representan los atributos (por ejemplo, nombre, apellido o dirección postal) del tipo.

**Parámetros adicionales**

Además de la configuración estándar descrita hasta ahora, puede definir opciones de configuración personalizadas adicionales que son específicos de la instancia del conector. Estos parámetros se pueden especificar en los niveles del conector, partición o paso de ejecución y se puede acceder a ellos desde el script de Windows PowerShell competente. Las opciones de configuración personalizadas pueden almacenarse en la base de datos del servicio de sincronización como texto sin formato o pueden cifrarse. El servicio de sincronización automáticamente cifra y descifra proteger las opciones de configuración seguras cuando sea necesario.

Para especificar opciones de configuración personalizadas, separe el nombre de cada parámetro por una coma (,).

Para tener acceso a las opciones de configuración personalizadas de un script, debe agregar un carácter de subrayado (\_) después del nombre y el ámbito del parámetro (Global, Partition o RunStep). Por ejemplo, para acceder el parámetro Global FileName, use este fragmento de código: `$ConfigurationParameters["FileName_Global"].Value`

### Capacidades

La pestaña Funcionalidades del Diseñador de agente de administración define el comportamiento y la funcionalidad del conector. Las selecciones realizadas en esta pestaña no pueden modificarse una vez creado el conector. La tabla siguiente enumera cada una de las opciones de funcionalidad.

![Capacidades](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Capacidad | Descripción |
| --- | --- |
| [Estilo de nombre distintivo][dnstyle] | Indica si el conector admite nombres distintivos y si es así, de qué estilo. |
| [Tipo de exportación][exportT] | Determina los tipos de objetos presentes en el script de exportación. <li>AttributeReplace: incluye el conjunto completo de valores de un atributo multivalor cuando cambia el atributo.</li><li>AttributeUpdate: incluye solo los deltas de un atributo multivalor cuando cambia el atributo.</li><li>MultivaluedReferenceAttributeUpdate: incluye un conjunto completo de valores de atributos multivalores sin referencia y deltas solo para atributos de referencia multivalor.</li><li>ObjectReplace: incluye todos los atributos de un objeto cuando cambia cualquier atributo</li> |
| [Normalización de datos][DataNorm] | Indica al servicio de sincronización que normalice los atributos de anclaje antes de que se proporcionen a los scripts. |
| [Confirmación de objeto][oconf] | Configura el comportamiento de importación pendiente en el servicio de sincronización. <li>Normal: comportamiento predeterminado que espera que todos los cambios exportados se confirmen mediante la importación</li><li>NoDeleteConfirmation: cuando se elimina un objeto, no hay ninguna importación pendiente generada.</li><li>NoAddAndDeleteConfirmation: cuando un objeto se crea o se elimina, no hay ninguna importación pendiente generada.</li>
| Usar el nombre distintivo como delimitador | Si se establece el estilo de nombre distintivo en LDAP, el atributo de anclaje del espacio del conector también es el nombre distintivo. |
| Operaciones simultáneas de varios conectores | Cuando está activada, se pueden ejecutar simultáneamente varios conectores de Windows PowerShell. |
| Particiones | Cuando está activada, el conector admite varias particiones y la detección de particiones. |
| Jerarquía | Cuando está activada, el conector admite una estructura jerárquica de estilo LDAP. |
| Habilitar importación | Cuando está activada, el conector importará datos a través de scripts de importación. |
| Habilitar importación diferencial | Cuando está activada, el conector puede solicitar los valores diferenciales de los scripts de importación. |
| Habilitar exportación | Cuando está activada, el conector exportará datos a través de scripts de exportación. |
| Habilitar exportación completa | Cuando está activada, los scripts de exportación admiten la exportación del espacio del conector completo. Para usar esta opción, también debe estar activada la opción Habilitar exportación.|
| No hay valores de referencia en el primer paso de exportación | Cuando está activada, se exportan los atributos de referencia en un segundo paso de exportación. |
| Habilitar cambio de nombre de objeto | Cuando está activada, se pueden modificar los nombres distintivos. |
| Eliminar-agregar al reemplazar | Cuando se activa, las operaciones de eliminar-agregar se exportan como un único reemplazo. |
| Habilitar operaciones de contraseña | Cuando está activada, se admiten los scripts de sincronización de contraseña. |
| Habilitar contraseña de exportación en el primer paso | Cuando está activada, se exportan las contraseñas establecidas durante el aprovisionamiento cuando se crea el objeto. |

### Parámetros globales

La pestaña Parámetros globales del Diseñador de agente de administración permite al administrador configurar cada script de Windows PowerShell que ejecutará el conector así como los valores globales para las opciones de configuración personalizadas definidas en la pestaña Conectividad.

**Detección de partición**

Una partición es un espacio de nombres independiente dentro de un esquema compartido. Por ejemplo en Active Directory, cada dominio es una partición dentro de un bosque. Una partición es la agrupación lógica de las operaciones de importación y exportación. La importación y exportación tienen particiones como contexto y se deben realizar todas las operaciones en este contexto. Se supone que las particiones representan una jerarquía en LDAP. El nombre distintivo de una partición se usa en la importación para comprobar que todos los objetos devueltos están dentro del ámbito de una partición. El nombre distintivo de la partición también se usa durante el aprovisionamiento del metaverso al espacio de conector para determinar con qué partición de un objeto debe asociarse durante la exportación.

El script de detección de partición recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector.
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |

El script debe devolver o bien un único objeto [Partition][part] o un elemento List[T] de objetos Partition en la canalización.

**Detección de jerarquía**

El script de detección de jerarquía solo se usa cuando la funcionalidad del estilo de nombre distintivo es LDAP. El script se usa para permitir a los administradores buscar y seleccionar un conjunto de contenedores que se considerarán dentro o fuera del ámbito de las operaciones de importación y exportación. El script solo debe proporcionar una lista de nodos que son elementos secundarios directos del nodo raíz proporcionado en el script.

El script de detección de jerarquía recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| ParentNode | [HierarchyNode][hn] | El nodo raíz de la jerarquía bajo el cual el script debe devolver los elementos secundarios directos. |

El script debe devolver un único objeto HierarchyNode secundario o un elemento List[T] de objetos HierarchyNode secundarios a la canalización.

#### Importación

Los conectores que admiten operaciones de importación deben implementar tres scripts.

**Begin Import**

El script de inicio de importación se ejecuta al principio de un paso de ejecución de importación. Durante este paso, puede establecer una conexión a los sistemas de origen y llevar a cabo los pasos preparatorios necesarios antes de importar datos desde el sistema conectado.

El script de inicio de importación recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa al script del tipo de ejecución de importación (diferencial o completo), partición, jerarquía, marca de agua y tamaño de página esperado.
| Tipos | [Esquema][schema] | El esquema del espacio del conector que se va a importar. |

El script debe devolver un único objeto [OpenImportConnectionResults][oicres] a la canalización. El código de ejemplo siguiente muestra cómo devolver un objeto OpenImportConnectionResults a la canalización:

`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Import Data**

El conector llama al script de importación de datos hasta que el script indique que ya no hay más datos para importar y el servicio de sincronización no necesita solicitar ninguna importación completa de objetos durante una importación diferencial. El conector de Windows PowerShell tiene un tamaño de página de 9 999 objetos. Si el script va a devolver más de 9 999 objetos para importar, debe admitir la paginación. El conector expone una propiedad de datos personalizados que se puede usar para almacenar una marca de agua, de tal forma que cada vez que se llame al script, este reanuda la importación de objetos donde se quedó.

El script de importación de datos recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| GetImportEntriesRunStep | [ImportRunStep][irs] | Almacena la marca de agua (CustomData) que se puede usar durante las importaciones paginadas y las importaciones diferenciales. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa al script del tipo de ejecución de importación (diferencial o completo), partición, jerarquía, marca de agua y tamaño de página esperado. |
| Tipos | [Esquema][schema] | El esquema del espacio del conector que se va a importar. |

El script de importación debe escribir un objeto List[[CSEntryChange] [csec]] en la canalización. Esta colección se compone de atributos de CSEntryChange que representan cada objeto que se va a importar. Durante una ejecución de importación completa, esta colección debe tener un conjunto completo de objetos CSEntryChange que tengan todos los atributos para cada objeto individual. Durante una importación diferencial, el objeto CSEntryChange debe contener los valores diferenciales de nivel de atributo para cada objeto que se va a importar o una representación completa de los objetos que han cambiado (modo de reemplazo).

**End Import**

Al acabar la ejecución de importación, se ejecutará el script de finalización de importación. Este script debe realizar las tareas de limpieza necesarias (por ejemplo, cerrar las conexiones a los sistemas, responder a errores, etc.).

El script de finalización de importación recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa al script del tipo de ejecución de importación (diferencial o completo), partición, jerarquía, marca de agua y tamaño de página esperado. |
| CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informa al script del motivo de la finalización de la importación. |

El script debe devolver un único objeto [CloseImportConnectionResults][cicres] a la canalización. El código de ejemplo siguiente muestra cómo devolver un objeto CloseImportConnectionResults a la canalización: `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### Exportación

De manera idéntica a la arquitectura de importación del conector, los conectores que admiten la exportación deben implementar tres scripts.

**Begin Export**

El script de inicio de exportación se ejecuta al principio de un paso de ejecución de exportación. Durante este paso, puede establecer una conexión a los sistemas de origen y llevar a cabo los pasos preparatorios necesarios antes de exportar datos desde el sistema conectado.

El script de inicio de exportación recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa al script del tipo de ejecución de exportación (diferencial o completo), partición, jerarquía y tamaño de página esperado. |
| Tipos | [Esquema][schema] | El esquema del espacio del conector que se va a exportar. |

El script no debe devolver ningún resultado a la canalización.

**Export Data**

El servicio de sincronización llamará al script de exportación de datos tantas veces como sea necesario para procesar todas las exportaciones pendientes. Dependiendo de si el espacio del conector tiene más exportaciones pendientes que el tamaño de la página del conector, de la presencia de los atributos de referencia o de contraseñas, puede llamarse varias veces al script de exportación de datos y posiblemente varias veces para el mismo objeto.

El script de exportación de datos recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector.|
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad.|
| CSEntries | IList[CSEntryChange][csec] | Lista de todos los objetos del espacio del conector con las exportaciones pendientes para su procesamiento durante este paso. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa al script del tipo de ejecución de exportación (diferencial o completo), partición, jerarquía y tamaño de página esperado. |
| Tipos | [Esquema][schema] | El esquema del espacio del conector que se va a exportar. |

El script de exportación de datos debe devolver un objeto [PutExportEntriesResults][peeres] a la canalización. Este objeto no necesita incluir la información de resultados para cada conector exportado a menos que se produzca un error o un cambio en el atributo delimitador.

El código de ejemplo siguiente muestra cómo devolver un objeto PutExportEntriesResults a la canalización: `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**End Export**

Al acabar la ejecución de la importación, se ejecutará el script de finalización de exportación. Este script debe realizar las tareas de limpieza necesarias (por ejemplo, cerrar las conexiones a los sistemas, responder a errores, etc.).

El script de finalización de exportación recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa al script del tipo de ejecución de exportación (diferencial o completo), partición, jerarquía y tamaño de página esperado. |
| CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informa al script del motivo de la finalización de la exportación. |

El script no debe devolver ningún resultado a la canalización.

#### Sincronización de contraseñas

Se pueden usar los conectores de Windows PowerShell como destino para los cambios o restablecimientos de contraseña.

El script de contraseña recibe los parámetros siguientes del conector:

| Nombre | Tipo de datos | Descripción |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][cadena, [ConfigParameter][cp]] | Tabla de parámetros de configuración para el conector. |
| Credential: | [PSCredential][pscred] | Contiene las credenciales especificadas por el administrador en la pestaña Conectividad. |
| Partition | [Partition][part] | Partición del directorio en el que se encuentra CSEntry. |
| CSEntry | [CSEntry][cse] | Entrada del espacio de conector para el objeto que recibe un cambio o restablecimiento de contraseña. |
| OperationType | String | Indica si la operación es un restablecimiento (**SetPassword**) o un cambio (**ChangePassword**). |
| PasswordOptions | [PasswordOptions][pwdopt] | Marcas que especifican el comportamiento de restablecimiento de contraseña pretendido. Este parámetro solo está disponible si OperationType es **SetPassword**. |
| OldPassword | String | Se rellena con la contraseña anterior del objeto para cambios de contraseña. Este parámetro solo está disponible si OperationType es **ChangePassword**. |
| NewPassword | String | Se rellena con la nueva contraseña del objeto que debe establecer el script. |

El script de contraseña no devuelve ningún resultado a la canalización de Windows PowerShell. Si se produce un error en el script de contraseña, este producirá una de las excepciones siguientes para informar al servicio de sincronización acerca del problema:

- [PasswordPolicyViolationException][pwdex1]\: se inicia si la contraseña no cumple la directiva de contraseñas del sistema conectado.
- [PasswordIllFormedException][pwdex2]\: se inicia si no se acepta la contraseña en el sistema conectado.
- [PasswordExtension][pwdex3]\: se inicia para todos los demás errores del script de contraseña.

## Conectores de ejemplo

Para una introducción general de los conectores de ejemplo disponibles, consulte [Windows PowerShell Connector Sample Connector Collection][samp].

## Otras notas

### Configuración adicional para la suplantación

Conceda al usuario que va a suplantar los permisos siguientes en el servidor del servicio de sincronización:

Acceso de lectura a las claves del Registro siguientes:

- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Software\\Microsoft\\PowerShell
- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Environment

Para determinar el identificador de seguridad (SID) de la cuenta de servicio del servicio de sincronización, ejecute los siguientes comandos de PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain><username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acceso de lectura a las carpetas del sistema de archivo siguientes:

- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\Extensions
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\ExtensionsCache
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\MaData<ConnectorName>

Sustituya el nombre del conector de Windows PowerShell por el marcador de posición <ConnectorName>.

## Solución de problemas

-	Para más información acerca de cómo habilitar el registro para solucionar problemas del conector, consulte [How to Enable ETW Tracing for FIM 2010 R2 Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291

<!---HONumber=AcomDC_0128_2016-->
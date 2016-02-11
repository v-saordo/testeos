<properties
   pageTitle="Azure AD Connect Sync: Conector de LDAP genérico | Microsoft Azure"
   description="En este artículo se describe cómo configurar el conector de LDAP genérico de Microsoft."
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

# Referencia técnica del conector de LDAP genérico

En este artículo se describe el conector de LDAP genérico. El artículo se aplica a los siguientes productos:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Debe usar la revisión 4.1.3461.0 o posterior ([KB2870703](https://support.microsoft.com/kb/2870703)).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Al hacer referencia a la RFC del IETF, este documento está utilizando el formato (RFC [número de RFC]/[sección en el documento RFC]), por ejemplo, (RFC 4512/4.3). Puede encontrar más información en http://tools.ietf.org/html/rfc4500 (tiene que reemplazar 4500 con el número correcto de RFC).

## Información general sobre el conector de LDAP genérico

El conector de LDAP genérico le permite integrar el servicio de sincronización con un servidor LDAP v3.

Ciertas operaciones y elementos de esquema, como los necesarios para realizar la importación diferencial, no se especifican en las RFC de IETF. Para estas operaciones se admiten solo directorios LDAP especificados explícitamente.

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

| Característica | Soporte técnico |
| --- | --- |
| Origen de datos conectado | El conector es compatible con todos los servidores LDAP v3 (compatibles con RFC 4510). Se ha probado con los siguientes: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Catálogo global de Microsoft Active Directory (AD GC) </li><li>389 Directory Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (anteriormente Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>Directorios destacados no compatibles: <li>Servicios de dominio de Microsoft Active Directory (AD DS) [Utilice el conector Active Directory integrado en su lugar]</li><li>Oracle Internet Directory (OID)</li> |
| Escenarios | <li>Administración del ciclo de vida de objetos</li><li>Administración de grupos</li><li>Administración de contraseñas</li> |
| Operaciones |Se admiten las siguientes operaciones en todos los directorios LDAP: <li>Importación completa</li><li>Exportación</li>Las siguientes operaciones solo se admiten en directorios especificados:<li>Importación diferencial</li><li>Establecer contraseña, Cambiar contraseña</li> |
| Esquema | <li>Se ha detectado un esquema en el esquema LDAP (RFC3673 y RFC4512/4.2)</li><li>Es compatible con clases estructurales, clases auxiliares y la clase de objetos extensibleObject (RFC4512/4.3)</li>

### Compatibilidad con importación diferencial y administración de contraseñas

Directorios compatibles con la importación diferencial y la administración de contraseñas:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con la operación Establecer contraseña
- Catálogo global de Microsoft Active Directory (AD GC)
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con la operación Establecer contraseña
- 389 Directory Server
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Apache Directory Server
    - No es compatible con la importación diferencial ya que este directorio no tiene un registro de cambios persistente
    - Es compatible con la operación Establecer contraseña
- IBM Tivoli DS
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Isode Directory
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Novell eDirectory y NetIQ eDirectory
    - Compatible con las operaciones Agregar, Actualizar y Cambiar nombre de la importación diferencial
    - No es compatible con operaciones Eliminar para importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Open DJ
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Open DS
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- Open LDAP (openldap.org)
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con la operación Establecer contraseña
    - No admite la operación Cambiar contraseña
- Oracle (anteriormente Sun) Directory Server Enterprise Edition
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
- RadiantOne Virtual Directory Server (VDS)
    - Debe utilizar la versión 7.1.1 o superior
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña
-  Sun One Directory Server
    - Es compatible con todas las operaciones de la importación diferencial
    - Es compatible con las operaciones Establecer contraseña y Cambiar contraseña

### Requisitos previos

Antes de usar el conector, asegúrese de que tiene lo siguiente en el servidor de sincronización, además de cualquier revisión mencionada antes:

- Microsoft .NET 4.5.2 Framework o posterior

### Detección del servidor LDAP

El conector se basa en una variedad de técnicas para detectar e identificar el servidor LDAP. El conector utiliza el DSE de raíz para buscar el nombre del proveedor y la versión e inspecciona el esquema para buscar objetos únicos y atributos que se sabe que existen en determinados servidores LDAP. Estos datos, si existen, se utilizan para rellenar previamente las opciones de configuración del conector.

### Permisos del origen de datos conectado

Para realizar operaciones de importación y exportación en los objetos del directorio conectado, la cuenta del conector debe tener permisos suficientes. El conector necesitará escribir permisos para poder exportar y permisos de lectura para poder importar. La configuración de permiso se realiza dentro de las experiencias de administración del propio directorio de destino.

### Puertos y protocolos

El conector utilizará el número de puerto especificado en la configuración; este número de forma predeterminada es 389 para LDAP y 636 para LDAPS.

Para LDAPS, debe utilizar SSL 3.0 o TLS. SSL 2.0 no es compatible y no se puede activar.

### Características y controles necesarios

Los siguientes controles y características LDAP deben estar disponibles en el servidor LDAP para que el conector funcione correctamente:

- 1\.3.6.1.4.1.4203.1.5.3 filtros True/False

Habitualmente no se notifica que el filtro True/False es compatible con directorios LDAP y puede que aparezca en la **página Global** en **Características obligatorias no encontradas**. Se utiliza para crear filtros **OR** en las consultas LDAP, por ejemplo, al importar varios tipos de objeto. Si puede importar más de un tipo de objeto, eso significa que el servidor LDAP es compatible con este filtro

Si utiliza un directorio donde un identificador único es el delimitador, también debe estar disponible lo siguiente (consulte sección [Configuración de delimitadores](#configure-anchors) más adelante en este artículo para más información):

- 1\.3.6.1.4.1.4203.1.5.1 todos los atributos operativos

Si el directorio tiene más objetos de los que caben en una llamada al directorio, se recomienda utilizar la paginación. Para que funcione la paginación, necesitará una de las siguientes opciones:

**Opción 1:**

- 1\.2.840.113556.1.4.319 pagedResultsControl

**Opción 2:**

- 2\.16.840.1.113730.3.4.9 VLVControl
- 1\.2.840.113556.1.4.473 SortControl

Si ambas opciones están habilitadas en la configuración del conector, se utilizará pagedResultsControl.

- 1\.2.840.113556.1.4.417 ShowDeletedControl

ShowDeletedControl sólo se utiliza con el método de importación diferencial USNChanged para poder ver los objetos eliminados.

El conector intenta detectar si las opciones están presentes en el servidor. Si no se pueden detectar las opciones, aparecerá una advertencia en la página Global en las propiedades del conector. No todos los servidores LDAP presentarán todos los controles y características que admiten e incluso aunque apareciera esta advertencia el conector podría funcionar sin problemas.

### Importación diferencial

La importación diferencial sólo está disponible cuando se ha detectado un directorio de soporte. Actualmente se utilizan los siguientes métodos:

- Registro de accesos LDAP. Consulte [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](Registro de http://www.openldap.org/doc/admin24/overlays.html#Access)
- Registro de cambios LDAP. Consulte [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Marca de tiempo. Para Novell/NetIQ eDirectory el conector utilizará la última fecha y hora para obtener objetos creados y actualizados. Novell/NetIQ eDirectory no proporcionan un medio equivalente para recuperar objetos eliminados. Esta opción también se puede utilizar si ningún otro método de importación diferencial está activo en el servidor LDAP. Esta opción no podrá importar los objetos eliminados.
- USNChanged. Consulte: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### No compatible

No se admiten las siguientes características LDAP:

- Referencias LDAP entre servidores (RFC 4511/4.1.10)

## Creación de un nuevo conector

Para crear un conector de LDAP genérico, en **Servicio de sincronización**, seleccione **Agente de administración** y **Crear**. Seleccione el conector **LDAP genérico (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### Conectividad

En la página Conectividad, debe especificar la información de host, puerto y enlace. Según cuál sea el enlace seleccionado, puede que se suministre información adicional en las secciones siguientes.

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- El valor de tiempo de espera de la conexión solo se utiliza para la primera conexión al servidor cuando se detecta el esquema.
- Si el enlace es anónimo, no se utiliza ningún nombre de usuario o contraseña ni certificados.
- Para otros enlaces, escriba la información ya sea mediante nombre de usuario y contraseña o bien mediante la selección de un certificado.
- Si usa Kerberos para autenticar, proporcione también el dominio kerberos o el dominio del usuario.

El cuadro de texto **Alias de atributo** se utiliza para los atributos definidos en el esquema con la sintaxis RFC4522. Estos no se pueden detectar durante la detección del esquema y el conector necesita ayuda para identificarlos. Por ejemplo, es necesario escribir lo siguiente en el cuadro de texto de alias de atributos para identificar correctamente el atributo userCertificate como un atributo binario:

`userCertificate;binary`

El siguiente es un ejemplo de cómo podría ser esto:

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Seleccione la casilla **Incluir atributos operativos en el esquema** para incluir también los atributos creados por el servidor. Estos incluyen atributos como la fecha y la hora de creación del objeto y la de la última actualización.

Seleccione **Incluir atributos extensibles en el esquema** si se utilizan objetos extensibles (RFC4512/4.3) y al habilitar esta opción permitirá que todos los atributos se utilicen en todos los objetos. Al seleccionar esta opción hará que el esquema sea muy grande por lo que, a menos que el directorio conectado utilice esta característica, le recomendamos que mantenga la opción desactivada.

### Parámetros globales

En la página Parámetros globales, puede configurar el nombre completo del registro de cambios diferencial y características adicionales de LDAP. La página se rellenará automáticamente con la información proporcionada por el servidor LDAP.

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La sección superior muestra la información proporcionada por el propio servidor, como, por ejemplo, el nombre del servidor. El conector también comprobará que los controles obligatorios estén presentes en el DSE de raíz. Si no se muestran, aparecerá una advertencia. Algunos directorios LDAP no mostrarán todas las características en el DSE de raíz y es posible que el conector funcione sin problemas incluso si aparece una advertencia.

Las casillas de **controles compatibles** controlan el comportamiento de ciertas operaciones:

- Con la eliminación de árbol seleccionada, se eliminará una jerarquía con una llamada LDAP. Con la eliminación de árbol no seleccionada, el conector realizará una eliminación recursiva si es necesario.
- Con los resultados paginados seleccionados el conector realizará importaciones paginadas con el tamaño especificado en los pasos de ejecución.
- Las opciones VLVControl y SortControl son una alternativa a pagedResultsControl a la hora de leer los datos del directorio LDAP.
- Si ninguna de las tres opciones (pagedResultsControl, VLVControl y SortControl) está seleccionada el conector importará todos los objetos en una sola operación, lo que podría producir un error si es un directorio grande.
- ShowDeletedControl solo se utiliza cuando el método de importación diferencial es USNChanged.

El DN del registro de cambios es el contexto de nomenclatura utilizado por el registro de cambios diferencial, por ejemplo, **cn = changelog**. Debe especificar este valor para poder hacer importación diferencia.

La siguiente es una lista de los nombres distintivos del registro de cambios predeterminado:

| Directorio | Registro de cambios diferencial |
| --- | --- |
| AD LDS y AD GC de Microsoft | Detectado automáticamente. USNChanged. |
| Apache Directory Server | No disponible. |
| Directory 389 | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog** |
| IBM Tivoli DS | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog** |
| Isode Directory | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog**
| Novell / NetIQ eDirectory | No disponible. Marca de tiempo. El conector utilizará la última fecha y hora de actualización para obtener los registros agregados y actualizados. |
| Open DJ/DS | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog** |
| Open LDAP | Registro de accesos. Valor predeterminado que se utiliza: **cn=accesslog** |
| Oracle DSEE | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog** |
| RadiantOne VDS | Directorio virtual. Depende del directorio conectado a VDS. |
| Sun One Directory Server | Registro de cambios. Valor predeterminado que se utiliza: **cn=changelog** |

El atributo de contraseña es el nombre de atributo que debe usar el conector para establecer la contraseña en las operaciones de cambio de contraseña y establecimiento de contraseña. De forma predeterminada se utiliza **userPassword** pero puede cambiarse si es necesario para un sistema LDAP en particular.

En la lista de particiones adicionales es posible agregar espacios de nombres adicionales que no se detectan automáticamente. Por ejemplo, esto puede utilizarse si varios servidores forman un clúster lógico y deben importarse al mismo tiempo. Igual que Active Directory puede tener varios dominios en un bosque, pero todos los dominios comparten un esquema, se puede simular lo mismo escribiendo los espacios de nombres adicionales en este cuadro. Cada espacio de nombres puede importar desde distintos servidores y se pueden configurar aún más en la página Configurar particiones y jerarquías. Utilice Ctrl + Intro para obtener una nueva línea.

### Configurar la jerarquía de aprovisionamiento

Esta página se utiliza para asignar el DN de componente, por ejemplo OU, al tipo de objeto que debe aprovisionarse, por ejemplo organizationalUnit.

![Jerarquía de aprovisionamiento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Puede configurar el conector para crear automáticamente una estructura cuando sea necesario mediante la configuración de la jerarquía de aprovisionamiento. Por ejemplo si hay un espacio de nombres dc = contoso, dc = com y se aprovisiona un nuevo objeto cn = Joe, ou = Seattle, c = US, dc = contoso, dc = com, en ese caso, el conector puede crear un nuevo objeto de país tipo para Estados Unidos y una organizationalUnit para Seattle si estos no están ya presentes en el directorio.

### Configurar particiones y jerarquías

En la página de particiones y jerarquías, seleccione todos los espacios de nombres con objetos que vaya a importar y exportar.

![Particiones](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada espacio de nombres también es posible establecer la configuración de conectividad que podría invalidar los valores especificados en la pantalla de conectividad. Si estos valores se dejan con su valor predeterminado en blanco, se utilizará la información de la pantalla de conectividad.

También es posible seleccionar los contenedores y las unidades organizativas desde los que el conector debe importar y hacia los que debe exportar.

### Configuración de delimitadores

Esta página siempre tiene un valor preconfigurado y no se puede cambiar. Si se ha identificado el proveedor del servidor y la versión este podría rellenarse con un atributo inmutable, por ejemplo, el GUID de un objeto. Si no se ha detectado o se sabe que no tiene un atributo inmutable, el conector utilizará el dn (nombre distintivo) como delimitador.

![delimitadores](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

La siguiente es una lista de servidores LDAP y el delimitador que se utiliza:

| Directorio | Atributo de delimitador |
| --- | --- |
| AD LDS y AD GC de Microsoft | objectGUID |
| 389 Directory Server | dn |
| Apache Directory | dn |
| IBM Tivoli DS | dn |
| Isode Directory | dn |
| Novell / NetIQ eDirectory | GUID |
| Open DJ/DS | dn |
| Open LDAP | dn |
| Oracle ODSEE | dn |
| RadiantOne VDS | dn |
| Sun One Directory Server | dn |

## Otras notas

Esta sección proporciona información de los aspectos que son específicos para este conector o aspectos que, por otras razones, es importante conocer.

### Importación diferencial

La marca de agua diferencial en Open LDAP es la fecha y hora UTC. Por este motivo, se deben sincronizar los relojes entre el servicio de sincronización de FIM y Open LDAP. Si no es así, se pueden omitir algunas entradas en el registro de cambios diferencial.

Para Novell eDirectory la importación diferencial no detectará las eliminaciones de objetos. Por ello es necesario ejecutar una importación completa periódicamente para buscar todos los objetos eliminados.

Para directorios con un registro de cambios diferencial basado en fecha y hora, se recomienda encarecidamente ejecutar una importación completa periódicamente para buscar eliminaciones y diferencias entre el servidor LDAP y lo que está actualmente en el espacio del conector.

## Solución de problemas

-	Para más información acerca de cómo habilitar el registro para solucionar problemas del conector, consulte [How to Enable ETW Tracing for FIM 2010 R2 Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0128_2016-->
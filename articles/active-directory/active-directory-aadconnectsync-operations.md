<properties
   pageTitle="Sincronización de Azure AD Connect: tareas y consideraciones operativas | Microsoft Azure"
   description="En este tema se describen las tareas operativas para la sincronización de Azure AD Connect y cómo prepararse para el funcionamiento de este componente."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Sincronización de Azure AD Connect: tareas y consideraciones operativas
El objetivo de este tema es describir las tareas operativas para la sincronización de Azure AD Connect.

## Modo provisional
Se puede usar el modo provisional para distintos escenarios como:

-	Alta disponibilidad.
-	Prueba e implementación de nuevos cambios de configuración.
-	Introducción de un nuevo servidor y baja del antiguo.

Con un servidor en modo provisional puede realizar cambios en la configuración y obtener una vista previa de los cambios antes de activar el servidor. También permite ejecutar una importación y sincronización completas para comprobar que se esperan todos los cambios antes de realizarlos en el entorno de producción.

Durante la instalación puede seleccionar que el servidor esté en **modo provisional**. Esto activará el servidor para la importación y sincronización pero no realizará ninguna exportación. Un servidor en modo provisional no ejecutará la sincronización de contraseñas ni habilitará la escritura diferida de contraseñas incluso aunque estas características estén seleccionadas. Cuando se deshabilita el modo provisional, el servidor iniciará la exportación, y habilitará la sincronización de contraseñas y la escritura diferida de contraseñas (si están habilitadas).

Un servidor en modo provisional seguirá recibiendo cambios de Active Directory y Azure AD. Por lo tanto, siempre tendrá una copia de los cambios más recientes y podrá asumir muy rápidamente las responsabilidades de otro servidor. Si realiza cambios de configuración en el servidor principal, es su responsabilidad realizar los mismos cambios en el servidor o servidores en modo provisional.

Para aquellos con conocimientos de tecnologías de sincronización anteriores, el modo provisional es diferente, ya que el servidor tiene su propia base de datos SQL. Esto permite que el servidor en modo provisional se encuentre en otro centro de datos.

### Comprobación de la configuración de un servidor
Para aplicar este método, siga estos pasos:

1. Preparación
2. Importación y sincronización
3. Verify
4. Cambio de servidor activo

**Preparación**

1. Instale Azure AD Connect, seleccione el **modo provisional** y anule la selección de **Iniciar la sincronización** en la última página del Asistente para instalación. Esto permitirá ejecutar el motor de sincronización de forma manual. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Cierre/inicie la sesión y en el menú de inicio, seleccione **Servicio de sincronización**.

**Importación y sincronización**

1. Seleccione **Conectores** y elija el primer conector de tipo **Servicios de dominio de Active Directory**. Haga clic en **Ejecutar**, seleccione **Importación completa** y **Aceptar**. Haga esto para todos los conectores de este tipo.
2. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **Importación completa** y **Aceptar**.
4. Asegúrese de que Conectores sigue aún seleccionado y para cada conector de tipo **Servicios de dominio de Active Directory**, haga clic en **Ejecutar**, seleccione **Sincronización diferencial** y **Aceptar**.
5. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **Sincronización diferencial** y, a continuación, Aceptar.

Ahora ha almacenado provisionalmente los cambios de exportación en Azure AD y AD local (si está usando la implementación híbrida de Exchange). Los siguientes pasos le permitirán inspeccionar lo que está a punto de cambiar antes de que comience realmente la exportación a los directorios.

**Verify**

1. Inicie un símbolo del sistema y vaya a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Ejecute: `csexport "Name of Connector" %temp%\export.xml /f:x`<BR/> El nombre del conector puede encontrarse en Servicio de sincronización. Tendrá un nombre similar a “contoso.com – AAD” para Azure AD.
3. Ejecute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Ahora tiene un archivo en %temp% denominado export.csv que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se van a exportar.
5. Realice los cambios necesarios en los datos o la configuración y ejecute estos pasos de nuevo (Importación y sincronización y Comprobación) hasta que se esperen los cambios que se van a exportar.

**Descripción del archivo export.csv**

La mayor parte del archivo se explica por sí solo. Algunas abreviaturas para comprender el contenido:

- OMODT: tipo de modificación de objeto. Indica si la operación en un nivel de objeto es Agregar, Actualizar o Eliminar.
- AMODT: tipo de modificación de atributo. Indica si la operación en un nivel de atributo es Agregar, Actualizar o Eliminar.

Si el atributo tiene varios valores, no se mostrarán todos los cambios. Solo será visible el número de valores agregados y quitados.

**Cambio de servidor activo**

1. En el servidor actualmente activo, desactive el servidor (DirSync/FIM/Azure AD Sync) para que no exporte a Azure AD o establézcalo en modo provisional (Azure AD Connect).
2. Ejecute el Asistente para instalación en el servidor en **modo provisional** y deshabilite el **modo provisional**. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Recuperación ante desastres
Parte del diseño de implementación es planear qué hacer en caso de que se produzca un desastre en el que se pierda el servidor de sincronización. Hay diferentes modelos de uso y cuál es el que se debe usar dependerá de varios factores como:

-	¿Cuál es su grado de tolerancia por no ser capaz de realizar cambios en objetos en Azure AD durante el tiempo de inactividad?
-	Si usa la sincronización de contraseñas, ¿aceptarán los usuarios que tienen que usar la antigua contraseña en Azure AD en caso de que la cambien de forma local?
-	¿Tiene una dependencia en operaciones en tiempo real, como la escritura diferida de contraseñas?

En función de las respuestas a estas preguntas y de la directiva de su organización, puede implementar una de las estrategias siguientes:

-	Recompilación si es necesario.
-	Servidor en espera de reserva, conocido como **modo provisional**.
-	Uso de máquinas virtuales.

Como la sincronización de Azure AD Connect tiene una dependencia en una base de datos SQL, debe revisar también la sección Alta disponibilidad de SQL si no usa SQL Express, que se incluye con Azure AD Connect.

### Recompilación si es necesario
Una estrategia viable es planear una recompilación del servidor si es necesario. En muchos casos, la instalación del motor de sincronización y la realización de la importación y sincronización iniciales se pueden completar en unas pocas horas. Si no hay un servidor de reserva disponible, es posible usar temporalmente un controlador de dominio para hospedar el motor de sincronización.

El servidor del motor de sincronización no almacena ningún estado acerca de los objetos, por lo que se puede recompilar la base de datos a partir de los datos de Active Directory y Azure AD. El atributo **sourceAnchor** se usa para unir los objetos de local y de la nube. Si recompila el servidor con objetos existentes locales y de la nube, el motor de sincronización los hará coincidir de nuevo en la reinstalación. Las acciones que necesita documentar y guardar son los cambios de configuración realizados en el servidor, como reglas de filtrado y de sincronización. Se deben aplicar de nuevo éstas antes de iniciar la sincronización.

### Servidor en espera de reserva - modo provisional
Si tiene un entorno más complejo, se recomienda tener uno o más servidores en espera. Durante la instalación puede habilitar un servidor que esté en **modo provisional**.

Para información adicional, vea [Modo provisional](#staging-mode).

### Uso de máquinas virtuales
Un método común y admitido es ejecutar el motor de sincronización en una máquina virtual. En caso de que el host tenga un problema, la imagen con el servidor del motor de sincronización se puede migrar a otro servidor.

### Alta disponibilidad de SQL
Si no se usa SQL Server Express que se incluye con Azure AD Connect, también se debe tener en cuenta la alta disponibilidad para SQL Server. La única solución de alta disponibilidad admitida son clústeres de SQL. Entre las soluciones no admitidas se incluyen creación de reflejos y siempre visible.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->
<properties
   pageTitle="Azure AD Connect: actualización desde una versión anterior | Microsoft Azure"
   description="Se explican los diferentes métodos para actualizar a la versión más reciente de Azure Active Directory Connect, como la actualización local y la migración oscilante."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="02/29/2016"
   ms.author="andkjell"/>

# Azure AD Connect: actualización de una versión anterior a la versión más reciente
En este tema se describen los distintos métodos que puede utilizar para actualizar la instalación de Azure AD Connect a la versión más reciente. Le recomendamos mantenerse al día con las versiones de Azure AD Connect.

Si quiere actualizar desde DirSync, consulte en su lugar [Azure AD Connect: actualización de Microsoft Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Hay algunas estrategias distintas para actualizar Azure AD Connect.

| Método | Descripción |
| --- | --- |
| [Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) | Para clientes con una instalación rápida, este es el método más fácil. |
| [Actualización local](#in-place-upgrade) | Si tiene un solo servidor, actualice la instalación en contexto en el mismo servidor. |
| [Migración oscilante](#swing-migration) | Con dos servidores, puede preparar uno de ellos con la nueva versión y cambiar el servidor activo cuando esté listo.

Para conocer los permisos necesarios, consulte [Azure AD Connect: cuentas y permisos](active-directory-aadconnect-accounts-permissions.md#upgrade).

## Actualización local
Una actualización local sirve para migrar de Azure AD Sync o Azure AD Connect. No sirve para DirSync ni para una solución con FIM + el Conector de Azure AD.

Este método es adecuado si tiene un único servidor y menos de unos 100 000 objetos. Después de la actualización, tendrá lugar una importación y una sincronización completas. De esta manera se garantiza que la nueva configuración se aplica a todos los objetos existentes en el sistema. Esta operación puede tardar unas horas según el número de objetos en el ámbito del motor de sincronización. La sincronización delta normal programada (cada 30 minutos de forma predeterminada) se suspende pero continúa la sincronización de contraseñas. Puede plantearse la posibilidad de realizar la actualización local durante un fin de semana.

![Actualización local](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si ha realizado cambios en las reglas de sincronización listas para usar, estas volverán a la configuración predeterminada tras la actualización. Para asegurarse de que la configuración se mantenga entre una actualización y otra, compruebe que los cambios se realizan como se describe en [Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## Migración oscilante
Si tiene una implementación compleja o muchos objetos, puede que realizar una actualización local en el sistema activo no resulte práctico. Esta operación podría tardar varios días para algunos clientes y durante este tiempo no se procesará ningún cambio delta.

En su lugar, se recomienda utilizar el método de la migración oscilante. Para este método necesitará (al menos) dos servidores, uno activo y otro de ensayo. El servidor activo (líneas azules continuas en la siguiente imagen) será responsable de la carga activa. El servidor de ensayo (líneas púrpuras discontinuas en la siguiente imagen) estará preparado con la nueva versión y, cuando esté completamente listo, se pondrá en activo. El servidor activo anterior, ahora con la versión anterior instalada, será el servidor de ensayo y se actualizará.

![Servidor provisional](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: Se ha observado que algunos clientes prefieren tener tres o cuatro servidores para esto. Puesto que el servidor de ensayo se está actualizando, durante este tiempo no tendrá un servidor de copia de seguridad para el caso de una [recuperación ante desastres](active-directory-aadconnectsync-operations.md#disaster-recovery). Se puede preparar un nuevo conjunto de servidores primarios/en espera con la nueva versión (hasta cuatro servidores como máximo) y asegurarse de que siempre haya un servidor de ensayo preparado para tomar el control.

Estos pasos también sirven para pasar de Azure AD Sync o de una solución con FIM + el Conector de Azure AD. Estos pasos no sirven para DirSync, pero el mismo método de migración oscilante (también llamada implementación paralela) con los pasos para DirSync se pueden encontrar en [Azure AD Connect: actualización de Microsoft Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### Pasos de la migración oscilante

1. Asegúrese de que el servidor activo y el servidor de ensayo utilizan la misma versión.
2. Si ha realizado una configuración personalizada y su servidor de ensayo no la tiene, siga los pasos que se indican en [Move custom configuration from active to staging server](#move-custom-configuration-from-active-to-staging-server) (Migración de la configuración personalizada del servidor activo al servidor de ensayo).
3. Actualice el servidor de ensayo a la versión más reciente.
4. Permita que el motor de sincronización ejecute la importación y la sincronización completas.
5. Compruebe que la nueva configuración no ha provocado ningún cambio inesperado usando los pasos que se indican en **Verify** en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si algo no está como se esperaba, corríjalo, ejecute la importación y la sincronización y compruebe hasta que los datos sean correctos. Estos pasos se pueden encontrar en el tema vinculado.
6. Cambie el servidor de ensayo para que sea el servidor activo. Este es el paso final: **Cambio de servidor activo** en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Actualice el servidor, ahora en modo de ensayo, a la última versión. Siga los mismos pasos que antes para actualizar los datos y la configuración.

### Traslado de la configuración personalizada del servidor activo al servidor provisional
Si ha realizado cambios en la configuración del servidor activo, debe asegurarse de que se aplican los mismos cambios en el servidor de ensayo.

Las reglas de sincronización personalizadas que haya creado se pueden mover con PowerShell. Otros cambios se deben aplicar de la misma forma en ambos sistemas.

Debe asegurarse de que la configuración sea la misma en ambos servidores:

- Conexión a los mismos bosques.
- Filtrado por dominio y unidad organizativa.
- Mismas características opcionales, como la sincronización de contraseña y la escritura diferida de contraseñas.

**Migración de las reglas de sincronización** Para migrar una regla de sincronización personalizada, haga lo siguiente:

1. Abra el **Editor de reglas de sincronización** en el servidor activo.
2. Seleccione la regla personalizada. Haga clic en **Exportar**. Se abrirá una ventana del Bloc de notas. Guarde el archivo temporal con una extensión PS1; así se convertirá en un script de PowerShell. Copie el archivo ps1 en el servidor de ensayo. ![Exportación de reglas de sincronización](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. El GUID del Conector será diferente en el servidor de ensayo. Para obtener el GUID, inicie el **Editor de reglas de sincronización**, seleccione una de las reglas listas para usar que representa el mismo sistema conectado y haga clic en **Exportar**. Reemplace el GUID del archivo PS1 por el GUID del servidor de ensayo.
4. En un símbolo del sistema de PowerShell, ejecute el archivo PS1. Esta acción creará la regla de sincronización personalizada en el servidor de ensayo.
5. Si tiene varias reglas personalizadas, repita lo mismo con todas ellas.

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->
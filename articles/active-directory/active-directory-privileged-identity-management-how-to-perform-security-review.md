<properties
   pageTitle="Privileged Identity Management de Azure: Realización de una revisión de seguridad"
   description="Obtenga información sobre cómo agregar roles a identidades con privilegios con la extensión de Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure: Realización de una revisión de seguridad


Resulta muy fácil revisar el acceso con privilegios una vez [iniciada una revisión de seguridad](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Para revisores: Aprobación o denegación del acceso

### Revisión por mí mismo
1. En el menú principal de PIM, haga clic en **Revisar acceso administrativo**. Aparecerá una lista de revisiones de seguridad.
2. Seleccione en la lista los **usuarios** cuyo acceso quiere cambiar. Nota: El acceso se cambiará realmente. Este proceso consiste simplemente en crear una lista de comprobación de aquellos cuyo acceso del rol se cambiaría. Después de seleccionar al menos un usuario, se habilitarán los botones **Aprobar acceso** y **Denegar acceso**.
3. Haga clic en **Aprobar acceso** o **Denegar el acceso** para los usuarios que seleccionó. Aparecerá una notificación en el menú principal del Portal de Azure y desaparecerá la lista de revisión. Cierre la hoja **Revisar roles de Azure AD**.

### Autorrevisión
1. El usuario recibirá un correo electrónico que indica que debe revisar su acceso. El correo electrónico contendrá un vínculo para iniciar sesión en el Portal de Azure.
2. Una vez allí, el usuario puede aprobar o denegar su propio acceso haciendo clic en los botones **Aprobar acceso** o **Denegar acceso**. Su nombre desaparecerá de la lista.

## Para administradores de revisión: Administración de revisiones de seguridad

## Finalización o detención de una revisión
1. Vuelva al panel de PIM.
2. En la lista **Revisiones de seguridad**, haga clic en la revisión de seguridad que quiere finalizar. Aparecerá la hoja de detalles de la revisión de seguridad.
3. Haga clic en **Detener revisión** para finalizar o detener la revisión. La revisión se archivará y la hoja desaparecerá.

## Exportación de una revisión
Puede exportar una revisión para su uso con Excel u otros programas que puedan utilizar archivos CSV.

1. Vuelva al panel de PIM.
2. Haga clic en la sección **Revisiones de seguridad** del panel. Aparecerá la hoja **Revisiones de seguridad**.
3. Haga clic en la revisión de seguridad que quiere exportar. Aparecerá la hoja de detalles de la revisión de seguridad.
4. Haga clic en el botón **Exportar**. Empezará a descargarse un archivo CSV.

## Eliminación de una revisión

> [AZURE.WARNING] No recibirá ninguna advertencia antes de que se produzca la eliminación, así que asegúrese de que realmente *quiere* eliminar esa revisión.

1. Vuelva al panel PIM.
2. Haga clic en la sección **Revisiones de seguridad** del panel. Aparecerá la hoja **Revisiones de seguridad**.
3. Haga clic en la revisión de seguridad que quiere eliminar. Aparecerá la hoja de detalles de la revisión de seguridad.
4. Haga clic en el botón **Eliminar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->
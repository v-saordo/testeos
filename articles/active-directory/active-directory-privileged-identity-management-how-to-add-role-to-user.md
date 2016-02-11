<properties
   pageTitle="Privileged Identity Management de Azure: Inicio de incorporación de un rol de usuario"
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

# Privileged Identity Management de Azure: Incorporación o eliminación de un rol de usuario

## Adición o eliminación de un rol de usuario
Hay varias maneras de navegar a la hoja **Agregar usuarios administrados** de la interfaz de PIM. A continuación se muestra la secuencia de clics para cada una:

- Panel > Usuarios en roles de administrador > Agregar o quitar
- Panel > Resumen de roles > Lista de todos los usuarios > Agregar o quitar
- Panel > haga clic en el rol de usuario en la tabla de roles (por ejemplo, Administrador global) > Agregar o quitar

## Adición de un usuario a un rol
Una vez que haya navegado a la hoja **Agregar usuarios administrados**:

1. Haga clic en **Seleccionar un rol**. Si navegó hasta aquí al hacer clic en un rol de usuario de la tabla de roles, el rol ya estará seleccionado.
2. Seleccione un rol de la lista de roles. Por ejemplo, **Administrador de contraseñas**; se abrirá la hoja **Seleccionar usuarios**.
3. Escriba el nombre del usuario en el campo de búsqueda. Si el usuario está en el directorio, aparecerán sus cuentas a medida que escriba el nombre, del mismo modo que aparecerán otros usuarios de nombre similar.
4. Seleccione el usuario en la lista y haga clic en **Listo**.
5. Haga clic en **Aceptar** para guardar la selección. El usuario que seleccionó aparecerá en la lista y el rol será temporal.
6. Si quiere que el rol sea permanente, haga clic en el usuario en la lista. La información del usuario aparecerá en una nueva hoja. Seleccione **convertir en permanente** en el menú de información de usuario.
7. Haga clic en **Activar** para iniciar una solicitud de activación de este rol para el usuario. En el campo de texto **Motivo de la solicitud**, escriba el motivo de la solicitud de activación. En este momento, el rol se activará automáticamente para este usuario y se enviará una notificación a los administradores globales.

## Eliminación de un usuario de un rol
1. Navegue al usuario en la lista de roles de usuario mediante una de las rutas de acceso descritas anteriormente.
2. Haga clic en el usuario en la lista de usuarios.
3. Haga clic en **Quitar**. Aparecerá un mensaje de confirmación.
4. Haga clic en **Sí** para quitar el rol del usuario.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->
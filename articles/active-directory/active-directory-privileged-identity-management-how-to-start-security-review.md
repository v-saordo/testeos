<properties
   pageTitle="Privileged Identity Management de Azure: Inicio de una revisión de seguridad"
   description="Obtenga información sobre cómo crear una revisión de seguridad para identidades con privilegios con la extensión de Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure: Inicio de una revisión de seguridad

## Inicio de una revisión de seguridad
Al final, podrá realizar revisiones de seguridad en otros lugares en el Portal de Azure. En este documento se tratan los pasos para iniciar una revisión de seguridad dentro de la interfaz de **Privileged Identity Management (PIM)**.

Puede que haya usuarios que no reconozca o quizás un usuario cambió de trabajo o de proyecto y en su nuevo puesto ya no necesita acceso con privilegios. Con el fin de reducir el riesgo asociado a estas asignaciones de roles "obsoletos", usted y otros administradores pueden revisar los roles que se han asignado a los usuarios iniciando una revisión de seguridad.

## Rutas de acceso para iniciar una revisión de seguridad
> [AZURE.NOTE] Si no creó ya un panel PIM en el Portal de Azure, vea los pasos de [Introducción a Privileged Identity Management de Azure](active-directory-privileged-identity-management-getting-started.md)

Desde el panel de PIM de Azure puede iniciar una vista siguiendo alguna de estas rutas de acceso:

- Panel > Revisiones de seguridad > Revisión > botón Revisar
- Panel > Roles > botón Revisar
- Panel > haga clic en el rol que va a se va a revisar en la lista de roles > botón Revisar

## Inicio de una revisión de seguridad

Al hacer clic en el botón **Revisar**, aparecerán las hojas **Comenzar a revisar un rol** y **Seleccionar un rol para su revisión** y se seleccionará el elemento **Seleccionar un rol para su revisión**.

### Selección del rol para revisar

1. Seleccione el rol en la lista de roles de la hoja **Seleccionar un rol para revisar**. Sólo puede elegir un rol a la vez. La hoja **Seleccionar un rol para revisar** se reemplazará por la hoja **Seleccionar revisores**. Al seleccionar revisores tiene dos opciones:
  - Yo: use esta característica si desea obtener una vista previa de cómo funcionan las revisiones de seguridad sin que intervengan otros administradores.
  - Autorrevisión por miembros del rol: utilice esta característica para que los usuarios revisen ellos mismos sus propias asignaciones de roles.
2. Seleccione cualquiera de estas opciones para comenzar a trabajar con los detalles de revisión. Aparecerá la hoja **Cambiar valores predeterminados**.

### Revisión por si mismo

1. Asigne un nombre a la revisión escribiéndolo en el campo **Nombre**. Se recomienda asignar a la revisión un nombre único que describa la revisión y que facilite su seguimiento.
2. Escriba una fecha de inicio para la revisión en el campo **Fecha de inicio**.
3. Escriba una fecha final para la revisión en el campo **Fecha de finalización**. Algunas cosas que debe tener en cuenta al establecer la fecha de finalización para la revisión son:
  - ¿A cuántas personas se revisa?
  - ¿Con qué rapidez los usuarios podrán agregar la extensión y completar la revisión?
4. Haga clic en el botón **Aceptar** de la hoja **Cambiar valores predeterminados**. Se cerrará.
5. Haga clic en el botón **Aceptar** de la hoja **Iniciar una revisión de un rol**. Se cerrará. Aparecerá una notificación en el menú principal del Portal de Azure. Actualice el panel haciendo clic en el botón **Actualizar** y la revisión de seguridad aparecerá en la sección **Revisiones de seguridad**.
6. Notifique a los usuarios del rol que tendrán que agregar la extensión y luego revisar su [propio acceso administrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

### Revisión por mí mismo

Si seleccionó la opción "Yo" como revisor, continúe con la revisión de seguridad. Para obtener más información sobre cómo completar la revisión, vea [Privileged Identity Management de Azure: Realización de una revisión de seguridad](active-directory-privileged-identity-management-how-to-perform-security-review.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Tabla de contenido de PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->
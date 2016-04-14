<properties
   pageTitle="Privileged Identity Management de Azure: Cómo usar el registro de auditoría"
   description="Obtenga información sobre cómo usar el registro de auditoría en la extensión de Privileged Identity Management de Azure."
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

# Privileged Identity Management de Azure: Cómo utilizar el registro de auditoría

Puede utilizar el registro de auditoría de Privileged Identity Management (PIM) para ver todas las asignaciones de usuario y las activaciones comprendidas en un período de tiempo determinado.

## Navegación al registro de auditoría
Puede acceder al registro de auditoría haciendo clic en el **historial de auditoría** del panel PIM.

## Gráfico del registro de auditoría
Puede utilizar el registro de auditoría para ver el total de activaciones, el número máximo de activaciones por día y el promedio de activaciones por día en un gráfico de líneas. También puede filtrar los datos por rol si hay más de un rol en el historial de auditoría.

Ordene por hora, acción o rol con los botones **hora**, **acción** o **rol**.

## Lista del registro de auditoría
Las columnas de la lista del registro de auditoría son:

- **Solicitante**: persona que solicita la activación del rol.
- **Usuario**: usuario de la activación del rol.
- **Rol**: rol asignado al usuario.
- **Acción**: acciones realizadas con el rol o usuario.
- **Hora**: momento en que se produjo la acción.
- **Razonamiento**: si durante la activación se escribió texto en el campo de motivo, aparecerá aquí.
- **Expiración**: si el año de expiración es 9999, el usuario tiene el rol de forma permanente.

## Filtrado del registro de auditoría

También puede filtrar la información que aparece en el registro de auditoría haciendo clic en el botón **Filtrar**. Aparecerá la hoja **Actualizar parámetros del gráfico**.

### Cambio del intervalo de fechas
Cambie el intervalo de tiempo del registro de auditoría seleccionando uno de los botones **Hoy**, **Semana anterior**, **Mes anterior** o **Personalizado**. Cuando elige el botón **Personalizado**, se le ofrecerá un campo de fecha **Desde** y un campo de fecha **Hasta** para especificar el intervalo de fechas que quiere para el registro. Puede especificar las fechas en formato MM/DD/AAAA o hacer clic en el icono de **calendario** y elegir la fecha en un calendario.

### Cambio de los roles incluidos en el registro

Active o desactive la casilla **Rol** situada junto a cada rol que quiera incluir o excluir del registro.

Una vez establecidos todos los filtros para el registro de auditoría, haga clic en Actualizar para filtrar los datos del registro. Si los datos no aparecen inmediatamente, actualice la página.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->
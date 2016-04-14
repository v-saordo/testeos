<properties
   pageTitle="Privileged Identity Management de Azure: Configuración de alertas de seguridad"
   description="Aprenda cómo configurar alertas de seguridad para la extensión de Privileged Identity Management de Azure."
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

# Privileged Identity Management de Azure: Configuración de alertas de seguridad

## Información general de alertas de seguridad
Privileged Identity Management de Azure (PIM) ofrece las siguientes alertas configurables. Las alertas de seguridad se pueden ver en la sección Alertas del panel de PIM.

| Alerta | Desencadenador |
| ------------- | ------------- |
| **Sospecha de ataque de activación permanente** | Un administrador activó su rol temporal fuera PIM. |
| **Activación sospechosa de renovación de roles con privilegios** | Había demasiadas reactivaciones del mismo rol con el tiempo permitido en la configuración. |
| **Uso sospechoso de usuario administrador global del honeytoken** | Se detectó el uso de un usuario "honeypot".|
| **Autenticación débil configurada para la activación del rol** | Hay roles sin MFA en la configuración. |
| **Administradores redundantes que aumentan la superficie expuesta a ataques** | Hay administradores temporales que no activaron sus roles dentro del número de días especificado en la configuración. |
| **Demasiados administradores globales que aumentan la superficie expuesta a ataques** | Existen más administradores globales de los permitidos en la configuración. |

## Configuración de alertas de seguridad

### Configuración de la alerta "Activación sospechosa de renovación de roles con privilegios"
1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Establezca el **Plazo de renovación de activación** ajustando el control deslizante o especificando el número de minutos en el campo de texto. El número máximo permitido 100.
4. Establezca el **Número de renovaciones de activación** en el plazo de renovación de activación ajustando el control deslizante o especificando el número de renovaciones en el campo de texto. El número máximo de renovaciones es 100.
5. Haga clic en **Guardar**.

### Configuración de la alerta "Administradores redundantes que aumentan la superficie expuesta a ataques"
1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Seleccione el número de días permitidos sin activación del rol ajustando el control deslizante o especificando el número de días en el campo de texto.
4. Haga clic en **Guardar**.

### Configuración de la alerta "Demasiados administradores globales que aumentan la superficie expuesta a ataques"

Esta alerta tiene dos ajustes que pueden desencadenar la alerta. El número mínimo de administradores globales desencadenará la alerta si es superior al número permitido de administradores. También se desencadenará la alerta si el porcentaje de administradores globales de la cantidad total de tipos de administradores es mayor que el porcentaje indicado en la configuración.

1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Establezca el **Número mínimo de administradores globales** ajustando el control deslizante o especificando el número en el campo de texto.
4. Establezca el **Porcentaje de Administradores globales** ajustando el control deslizante o especificando el número en el campo de texto.
5. Haga clic en **Guardar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->
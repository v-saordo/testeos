    <properties
	pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
	description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Adición de propietarios y usuarios a un laboratorio de desarrollo y pruebas

## Información general

El acceso a un laboratorio de desarrollo y pruebas se controla mediante el Control de acceso basado en rol (RBAC) de Azure. Busque [Control de acceso basado en rol (RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29) en el portal de vista previa de Azure para obtener más información.

Se concede acceso al laboratorio de desarrollo y pruebas a través de dos roles:

 - **Propietario**: los usuarios asignados al rol **propietario** en el nivel de suscripción de Azure tienen acceso completo al laboratorio, incluidas las funciones de administración y supervisión.

     >[AZURE.NOTE] Los roles **propietario** que se asignan en niveles RBAC diferentes al nivel de suscripción no se admiten en Laboratorio de desarrollo y pruebas. La asignación de un usuario al rol **Propietario** en el laboratorio de desarrollo y pruebas no se admite.

 -  **Usuario de laboratorio de desarrollo y pruebas**: los usuarios asignados al rol **Usuario de laboratorio de desarrollo y pruebas** pueden crear, actualizar y eliminar VM en el laboratorio especificado. Los usuarios pueden ser *internos* (miembro de Azure Active Directory para la suscripción) o *externos* (usuario que no es miembro de Azure AD, como un miembro de una organización asociada).
	-  Un rol **Usuario de laboratorio de desarrollo y pruebas** debe asignarse a través de los iconos **Agregar usuarios** del laboratorio.
	-  Los usuarios del rol **Usuario de laboratorio de desarrollo y pruebas** pueden realizar estas operaciones solo dentro del laboratorio al que están asignados. Por ejemplo, un **Usuario de laboratorio de desarrollo y pruebas** no puede crear una máquina virtual mediante el servicio Máquina virtual de la suscripción. La creación de una máquina virtual solo se permite desde la cuenta de Laboratorio de desarrollo y pruebas.
	- Los usuarios *externos* deben tener una cuenta en uno de los dominios de cuentas de Microsoft (es decir, @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com o cualquier variante de un país específico).

## Adición de un propietario al laboratorio

Laboratorio de desarrollo y pruebas considera que los propietarios de una suscripción de Azure que contiene laboratorios son los propietarios de esos laboratorios. Aunque puede agregar propietarios adicionales a un laboratorio de desarrollo y pruebas a través de la hoja del laboratorio en el portal de vista previa de Azure, esto no se admite actualmente.

Para agregar un propietario a una suscripción de Azure donde tiene laboratorios ya creados o va a crear nuevos laboratorios, siga estos pasos:

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

1. En el panel de navegación izquierdo, pulse **Suscripciones**.

	![Vínculo de suscripciones](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Pulse la suscripción que contendrá los laboratorios.

1. Pulse el icono **Acceso**.

	![Usuarios de acceso](./media/devtest-lab-add-devtest-user/access-users.png)

1. En la hoja **Usuarios**, pulse **Agregar**.

	![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En la hoja **Seleccionar un rol**, pulse **Propietario**.

1. En el cuadro de texto **Usuario** escriba el correo electrónico del usuario que desea agregar como propietario. Si no se encuentra el usuario, aparecerá un mensaje de error que explica el problema. Si se encuentra el usuario, dicho usuario se mostrará en el cuadro de texto **Usuario**.

1. Pulse el nombre de usuario encontrado.

1. Pulse **Seleccionar**.

1. Pulse **Aceptar** para cerrar la hoja **Agregar acceso**.

1. Cuando vuelva a la hoja **Usuarios**, verá que el usuario se ha agregado como propietario. Esta persona ahora será un propietario de cualquier laboratorio creado con esta suscripción y, por tanto, podrá realizar tareas de propietario.

## Adición de un usuario de Laboratorio de desarrollo y pruebas al laboratorio

Para agregar un usuario de Laboratorio de desarrollo y pruebas al laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

1. Pulse **Examinar**.

1. Pulse **Laboratorios de desarrollo y pruebas**.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Pulse el icono **Acceso**.

	![Acceso de usuario](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. En la hoja **Usuarios**, pulse **Agregar**.

	![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En la hoja **Seleccionar un rol**, pulse **Usuario de laboratorio de desarrollo y pruebas**.

1. En la hoja **Agregar usuarios**:

	1. La hoja **Agregar usuarios** mostrará una lista de usuarios integrados. Si el usuario que desea ya está en la lista, basta con pulsar la fila del usuario para seleccionarlo. Aparecerá una marca de verificación a la izquierda del usuario para indicar que este se ha seleccionado. Para seleccionar varios usuarios, mantenga presionada la tecla **& lt; Ctrl >** mientras hace clic en cada usuario. Para anular la selección de un usuario, mantenga presionada la tecla **& lt; Ctrl >** y haga clic en el usuario. Un contador en la parte inferior de la hoja indica el número de usuarios seleccionados.

	1. Si el usuario que desea no está en la lista, escriba una cuenta de correo electrónico de Microsoft válida en el cuadro de texto **Usuarios**. Si la dirección de correo electrónico es válida, el usuario se mostrará debajo del cuadro de texto **Usuario**. Basta con pulsar en él para seleccionarlo.

	1. Cuando haya seleccionado los usuarios que desea agregar al laboratorio, pulse **Seleccionar**.

	1. Pulse **Aceptar** para cerrar la hoja **Agregar acceso**.

	1. La hoja **Usuarios** muestra los roles y usuarios agregados.

<!---HONumber=AcomDC_0128_2016-->
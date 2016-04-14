<properties
   pageTitle="Obtención de la misma experiencia de Office 365 en cualquier dispositivo con Azure RemoteApp | Microsoft Azure"
   description="Aprenda a compartir cualquier aplicación de Office 365 con sus usuarios mediante Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="12/05/2015"
   ms.author="guscatal;elizapo"/>


# Obtención de la misma experiencia de Office 365 en cualquier dispositivo con Azure RemoteApp

Este artículo tratará cómo implementar Office 365 en cualquier dispositivo de su empresa. Los usuarios pueden obtener las mismas capacidades y experiencia de interfaz de usuario en Android, Apple y Windows.

Para conseguirlo, usaremos Azure RemoteApp hospedando Office 365 en máquinas virtuales escalables de Azure a las que pueden conectarse los usuarios. A este conjunto de máquinas virtuales lo denominamos "colección en la nube".

## Crear una colección en la nube

En primer lugar, tras crear una cuenta de Azure, navegue hasta **RemoteApp** haciendo clic en el vínculo situado a la izquierda. ![Visualización de RemoteApp de Azure en el Portal de Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continúe haciendo clic en **Nuevo** en la parte inferior y en "Creación rápida" para crear así una colección. Proporcione un nombre, la región, la suscripción, el plan y la imagen "Office Proffesional 2013" que proporcionamos. ![Cuadro de diálogo Crear](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Una vez que finalice el formulario, debería comenzar el proceso de creación de la colección. Esto puede tardar hasta una hora aproximadamente.

![En espera](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Cuando el proceso haya finalizado, tendrá un aspecto similar al siguiente. Si hacemos clic en **Publicación**, podemos ver que ya publicamos la mayoría de las aplicaciones de Office. ![Colección creada](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicaciones publicadas](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

En este momento puede agregar también otros usuarios que tengan acceso a esta colección haciendo clic en **Acceso de usuario**. ![Configurar el acceso del usuario](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Ahora vamos a intentar la conexión a Office 365.

## Conectarse a Office 365

Iremos a [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), nos desplazaremos hacia abajo y haremos clic en **Descargar clientes** para instalar el cliente de Azure RemoteApp en el dispositivo en que se encuentre. Las capturas de pantalla siguientes corresponden a Windows.

Cuando se inicie la aplicación, se le pedirá que inicie sesión con su cuenta Microsoft (antes llamada "Live ID"); por ahora, use la misma cuenta de Azure. Tras iniciar sesión, debe ver una notificación sobre nuevas invitaciones, haga clic en ella y verá una lista como la siguiente. Acepte la invitación dirigida al correo electrónico del propietario de su cuenta de Azure.

![Nueva invitación](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Este es lo que aparece cuando hay invitaciones nuevas.

![Aceptar una aplicación](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Después de aceptar la invitación, debe ver todas las aplicaciones de Office en el cliente de Azure RemoteApp.

![Lista de aplicaciones](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Al hacer clic en cualquiera de las aplicaciones, esta debe iniciarse en la máquina virtual de Azure y, con ello, todo estará configurado. ¡Disfrute!

![iniciando](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)

<!---HONumber=AcomDC_1210_2015-->
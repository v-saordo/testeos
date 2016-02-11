<properties pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: introducción | Microsoft Azure" description="Introducción a los Servicios de dominio de Azure Active Directory" services="active-directory-ds" documentationCenter="" authors="mahesh-unnikrishnan" manager="stevenpo editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: introducción

## Paso 4: Actualización de la configuración DNS para la red virtual de Azure
Ahora que ha habilitado correctamente los Servicios de dominio de Azure AD en el directorio, el siguiente paso es asegurarse de que los equipos de la red virtual puedan conectarse a estos servicios y consumirlos. Para ello, deberá actualizar la configuración del servidor DNS de la red virtual para que apunte a las direcciones IP en las que los Servicios de dominio de Azure AD estarán disponibles en la red virtual.

> [AZURE.NOTE] Después de habilitar los Servicios de dominio de Azure AD para el directorio, anote las direcciones IP de los Servicios de dominio de Azure AD mostrados en la pestaña **Configurar** del directorio.

Realice los siguientes pasos de configuración para actualizar la configuración del servidor DNS en la red virtual en la que ha habilitado los Servicios de dominio de Azure AD.

1. Navegue al **Portal de administración de Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Seleccione el nodo **Redes** en el panel izquierdo.

    ![Nodo Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. En la pestaña **Redes virtuales**, seleccione la red virtual en la que habilitó los Servicios de dominio de Azure AD para ver sus propiedades.
4. Haga clic en la pestaña **Configurar**.

    ![Nodo Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. En la sección **Servidores DNS**, escriba las direcciones IP de los Servicios de dominio de Azure AD.
6. Asegúrese de especificar las dos direcciones IP que se mostraron en la sección **Servicios de dominio** en la pestaña **Configurar** del directorio.
7. Haga clic en **Guardar** en el panel de tareas de la parte inferior de la página para guardar la configuración del servidor DNS para esta red virtual.

   ![Actualización de la configuración del servidor DNS para la red virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Después de actualizar la configuración del servidor DNS para la red virtual, las máquinas virtuales de la red pueden tardar un rato en recibir la configuración DNS actualizada. Si una máquina virtual no puede conectarse al dominio, puede vaciar la caché de DNS (por ejemplo, ipconfig/flushdns) en la máquina virtual, y así forzar una actualización de la configuración DNS en la máquina virtual.

---
[**Siguiente paso: Habilitación de la sincronización de contraseñas con los Servicios de dominio de Azure AD.**](active-directory-ds-getting-started-password-sync.md)

<!---HONumber=AcomDC_0128_2016-->
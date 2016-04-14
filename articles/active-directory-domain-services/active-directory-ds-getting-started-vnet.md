<properties
	pageTitle="Vista previa de los Servicios de dominio de Azure Active Directory: introducción | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: introducción

## Directrices para seleccionar una red virtual de Azure
Al seleccionar una red virtual para usar con los Servicios de dominio de Azure AD, tenga en cuenta las siguientes pautas:

- Asegúrese de seleccionar una red virtual de una región que sea compatible con los Servicios de dominio de Azure AD. Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que están disponibles los Servicios de dominio de Azure AD.
- Si planea usar una red virtual existente, asegúrese de que sea una red virtual regional. Las redes virtuales que usan el mecanismo de grupos de afinidad heredados no se puede usar con los Servicios de dominio de Azure AD. Deberá [migrar las redes virtuales heredadas a redes virtuales regionales](../virtual-networks-migrate-to-regional-vnet.md).
- Si planea usar una red virtual existente, asegúrese de que no haya ningún servidor DNS personalizado configurado para la red virtual. Los Servicios de dominio de Azure AD no admiten servidores DNS personalizados ni proporcionados por uno mismo.
- Si planea usar una red virtual existente, asegúrese de que no tenga un dominio existente con el mismo nombre de dominio disponible en esa red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Servicios de dominio de Azure AD con el mismo nombre de dominio (es decir, "contoso.com") en esa red virtual. Al intentar habilitar los Servicios de dominio de Azure AD, encontrará un error. Esto se debe a los conflictos de nombre en el nombre de dominio de esa red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.
- Seleccione la red virtual que hospeda o va a hospedar las máquinas virtuales que necesitan acceso a los Servicios de dominio de Azure AD. No podrá mover los Servicios de dominio a otra red virtual después de haber habilitado el servicio.
- No se admiten los Servicios de dominio de Azure AD con redes virtuales creadas mediante el Administrador de recursos de Azure. Puede [conectar una red virtual clásica a una red virtual de ARM](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md), a fin de utilizar los Servicios de dominio de Azure AD en una red virtual creada mediante el Administrador de recursos de Azure.


## Paso 2: Creación de una red virtual de Azure
El siguiente paso de configuración es crear una red virtual de Azure en la que quiere habilitar los Servicios de dominio de Azure AD. Si ya tiene una red virtual que quiere usar, puede omitir este paso.

> [AZURE.NOTE] Asegúrese de que la red virtual de Azure que cree o elija usar con los Servicios de dominio de Azure AD pertenezca a una región de Azure que sea compatible con dichos servicios. Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que están disponibles los Servicios de dominio de Azure AD.

Deberá anotar el nombre de la red virtual para así seleccionar la red virtual adecuada al habilitar los Servicios de dominio de Azure AD en un paso de configuración posterior.

Realice el siguiente paso de configuración para crear una red virtual de Azure en la que quiere habilitar los Servicios de dominio de Azure AD.

1. Navegue al **Portal de administración de Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Seleccione el nodo **Redes** en el panel izquierdo.
3. En la parte inferior de la página, haga clic en **NUEVO** en el panel de tareas.

    ![Nodo Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. En el nodo **Servicios de red**, seleccione **Red virtual**.
5. Haga clic en **Creación rápida** para crear una red virtual.

    ![Red virtual: creación rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Escriba un **Nombre** para la red virtual. También puede elegir configurar el **Espacio de direcciones** o el **Número máximo de VM** para esta red. Por ahora, puede dejar la configuración del servidor DNS establecida en 'Ninguna'. Esta configuración se actualizará después de habilitar los Servicios de dominio de Azure AD.
7. Asegúrese de seleccionar una región de Azure compatible en la lista desplegable **Ubicación**. Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que están disponibles los Servicios de dominio de Azure AD. Este es un paso importante. Si selecciona una red virtual de una región de Azure que no es compatible con los Servicios de dominio de Azure AD, no podrá habilitar el servicio en esa red virtual.
8. Haga clic en el botón **Crear una red virtual** para crear la red virtual.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**Paso siguiente: Habilitación de los Servicios de dominio de Azure AD.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=AcomDC_0211_2016-->
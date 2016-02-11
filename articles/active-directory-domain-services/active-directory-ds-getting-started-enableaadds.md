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
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: introducción

## Paso 3: Habilitación de los Servicios de dominio de Azure AD
En este paso puede habilitar los Servicios de dominio de Azure AD para su directorio. Realice los siguientes pasos de configuración para habilitar los Servicios de dominio de Azure AD para su directorio.

1. Navegue al **Portal de administración de Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. En el panel izquierdo, seleccione **Active Directory**.
3. Seleccione al inquilino (directorio) de Azure AD para el que quiere habilitar los Servicios de dominio de Azure AD.

    ![Selección de un directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Haga clic en la pestaña **Configurar**.

    ![Pestaña Configurar del directorio](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desplácese a una sección titulada **Servicios de dominio**.

    ![Sección de configuración de los Servicios de dominio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Mueva la opción de conmutación **Habilitar Servicios de dominio para este directorio** a **Sí**. Observará que en la página aparecen algunas opciones más de configuración para los Servicios de dominio de Azure AD.

    ![Habilitación de los Servicios de dominio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Al habilitar los Servicios de dominio de Azure AD para el inquilino, Azure AD genera y almacena los valores de hash de credenciales de Kerberos y NTLM que son necesarios para autenticar a los usuarios.

7. Especifique el **Nombre de dominio DNS de Servicios de dominio**.
   - El nombre de dominio predeterminado del directorio (es decir, que termina con el sufijo de dominio **. onmicrosoft.com**) se seleccionará de forma predeterminada.
   - La lista contiene todos los dominios que se han configurado para el directorio de Azure AD, incluidos también los dominios comprobados y sin comprobar que configura en la pestaña 'Dominios'.
   - Además, puede agregar también un nombre de dominio personalizado a esta lista con solo escribirlo.

     >[AZURE.WARNING] Asegúrese de que el prefijo de dominio del nombre de dominio que especifique (por ejemplo, "contoso" en el nombre de dominio 'contoso.local') tenga menos de 15 caracteres. No se puede crear un dominio de Servicios de dominio de Azure AD con un prefijo de dominio de más de 15 caracteres.

8. El siguiente paso consiste en seleccionar una red virtual en la que quiere que los Servicios de dominio de Azure AD estén disponibles. Seleccione la red virtual que acaba de crear en la lista desplegable **Conectar Servicios de dominio a esta red virtual**.
   - Asegúrese de que la red virtual que ha especificado pertenece a una región de Azure compatible con los Servicios de dominio de Azure AD.
   - Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que están disponibles los Servicios de dominio de Azure AD.

9. Cuando haya terminado de seleccionar las opciones anteriores, haga clic en **Guardar** en el panel de tareas de la parte inferior de la página para habilitar los Servicios de dominio de Azure AD.
10. La página mostrará un estado 'Pendiente...' mientras los Servicios de dominio de Azure AD se habilitan para su directorio.

    ![Habilitación de los Servicios de dominio: estado pendiente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Los Servicios de dominio de Azure AD proporcionan una alta disponibilidad para el dominio administrado. La primera vez que habilite los Servicios de dominio de Azure AD para su dominio, observará que las direcciones IP en las que están disponibles los Servicios de dominio en la red virtual se muestran de una en una. La segunda dirección IP se muestra al rato, en cuanto el servicio habilita la alta disponibilidad para el dominio. Cuando se configura la alta disponibilidad y está activa para su dominio, debe ver dos direcciones IP en la sección **Servicios de dominio** de la pestaña **Configurar**.

11. Al cabo de unos 20 o 30 minutos, verá la primera dirección IP en la que los Servicios de dominio están disponibles en la red virtual, en el campo **Dirección IP** de la página **Configurar**.

    ![Servicios de dominio habilitados: primera dirección IP aprovisionada](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. Cuando la alta disponibilidad está operativa para el dominio, verá dos direcciones IP en la página. Estas son las direcciones IP en las que los Servicios de dominio de Azure AD estarán disponibles en la red virtual seleccionada. Anote estas direcciones para que pueda actualizar la configuración de DNS de la red virtual. Este paso permite a las máquinas virtuales de la red virtual conectarse al dominio de cara para realizar operaciones como unirse a un dominio.

    ![Servicios de dominio habilitados: ambas direcciones IP aprovisionadas](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] En función del tamaño del directorio de Azure AD (número de usuarios, grupos etc.), el contenido del directorio tardará un rato en estar disponible en los Servicios de dominio de Azure AD. Este proceso de sincronización se produce en segundo plano. En el caso de directorios grandes con decenas de miles de objetos, todos los usuarios, pertenencias a grupos y credenciales tardan en sincronizarse y en estar disponibles en los Servicios de dominio de Azure AD uno o dos días.


---
[**Siguiente paso: Actualización de la configuración DNS para la red virtual de Azure.**](active-directory-ds-getting-started-dns.md)

<!---HONumber=AcomDC_0128_2016-->
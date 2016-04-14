<properties
	pageTitle="Qué es PowerApps Enterprise y cómo empezar | Microsoft Azure"
	description="Introducción a PowerApps Enterprise y creación del entorno del Servicio de aplicaciones"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# ¿Qué es Microsoft PowerApps Enterprise?

Microsoft PowerApps Enterprise es un nuevo servicio de Microsoft Azure. PowerApps Enterprise extiende la creación de aplicaciones móviles a los usuarios profesionales dentro de la empresa y permite a los administradores de TI administrar estrictamente estas aplicaciones.

Con una interfaz similar a Office, con cintas y fórmulas de Excel, los usuarios profesionales pueden crear aplicaciones que:

- Mostrar datos con gráficos de líneas, circulares y de barras, exactamente igual que en Excel.
- Crear interfaces de usuario con botones, mediante la inserción de texto y el formato de una fecha.
- Agregar controles de tipo test, que incluyen cuadros de lista, listas desplegables y botones de radio.
- Cargar imágenes, tomar una fotografía y reproducir archivos de audio y vídeo.
- Usar sistemas "back-end", como Excel y SQL Server, para mostrar y actualizar la información.
- Agregar conectores pregenerados de Servicio de aplicaciones a las aplicaciones que se conectan a programas PaaS como Twitter y SharePoint.

Los administradores de TI pueden administrar las aplicaciones que crean los usuarios profesionales de su empresa, por lo que pueden:

- Administrar estas aplicaciones y, además, administrar el acceso de usuario a las mismas.
- Crear API y conexiones a distintos orígenes de datos. 
- Administrar el acceso de usuario a las API y conexiones a estos orígenes de datos. 

## ¿Cómo empiezo?

En primer lugar, determine si debe crear un nuevo inquilino de Azure Active Directory (Azure AD). Si ya cuenta con un inquilino de AD, basta con habilitar PowerApps Enterprise en el Portal de Azure, agregar las API y conexiones y comenzar con la administración (desde el *paso 2* de este tema).

Si no tiene un inquilino de AD, cree uno, habilite PowerApps Enterprise en el Portal de Azure, agregue las API y conexiones y comience con la administración.

Los detalles se indican en este tema.

## Paso 1: Crear un inquilino de Azure AD nuevo o utilizar uno existente

Para comenzar a trabajar con PowerApps Enterprise, necesita un inquilino de Azure Active Directory (Azure AD). Un inquilino es una instancia dedicada del servicio de Azure AD.

Cuando la organización o empresa se suscribe en un servicio en la nube de Microsoft Azure, como Office 365 o Microsoft Intune, recibe y se convierte automáticamente en propietaria de un inquilino de AD. Cada inquilino de AD es distinto e independiente de los demás inquilinos de Azure AD.

Siga estos pasos para determinar si ya tiene un inquilino o para saber cómo crear uno nuevo.

#### Dispone de una suscripción de Office 365 existente
Si tiene una suscripción de Office 365 existente (o Microsoft Dynamic CRM Online, Enterprise Mobility Suite u otros servicios de Microsoft), tiene una suscripción gratis a Azure Active Directory. Puede usar Azure AD para crear y administrar cuentas de usuario y grupo. Si no puede iniciar sesión en el Portal de Azure, es probable que deba activar la suscripción. Para hacerlo, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/) y complete un proceso de registro único. Siga estos [pasos](https://technet.microsoft.com/library/dn832618.aspx) para obtener acceso al inquilino de Azure AD.

#### Tiene una suscripción de Azure asociada a una cuenta Microsoft.
Si se registró anteriormente a una suscripción de Azure con su cuenta Microsoft individual (Hotmail o Live), ya dispone de un inquilino. En el [Portal de Azure clásico](https://manage.windowsazure.com/), **Inquilino predeterminado** aparece en **Todos los elementos** y en **Active Directory**. Pueden usa este inquilino como considere oportuno, pero es posible que desee crear una cuenta de administrador organizativo.

Para ello, siga estos pasos. También es posible que quiera crear a un nuevo inquilino y crear un administrador en ese inquilino siguiendo un proceso similar.

1.	Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con su cuenta individual.
2.	Seleccione **Active Directory** en la barra de menús de la izquierda. 
3.	Seleccione **Directorio predeterminado** en la lista de directorios disponibles.
4.	Seleccione la pestaña **Usuarios** en la parte superior. Hay un usuario único que aparece con "Cuenta Microsoft" en la columna Con origen en.
5.	Seleccione **Agregar usuario** en la parte inferior. 
6.	En **Agregar formulario de usuario**, escriba estos detalles:  
	
	Propiedad | Descripción
--- | ---
Tipo de usuario | Nuevo usuario de la organización
Nombre de usuario | Elija un nombre de usuario para este administrador
Nombre/apellido/nombre para mostrar | Escriba los valores
Rol | Administrador global
Dirección de correo electrónico alternativa | Escriba el valor
Opcional | Habilite Multi-Factor Authentication  
	
	Seleccione el botón **CREAR** para completar y mostrar la contraseña temporal.

Cuando termine, anote esta contraseña temporal para el nuevo usuario administrativo. Para cambiar la contraseña temporal, inicie sesión en [https://login.microsoftonline.com](https://login.microsoftonline.com) con esta cuenta de usuario nueva y cambie la contraseña. También puede enviar la contraseña directamente al usuario mediante un correo electrónico alternativo.


#### Dispone de una suscripción de Azure existente asociada a una cuenta de organización
Si se registró anteriormente a una suscripción de Azure con su cuenta de organización, ya dispone de un inquilino. En el [Portal de Azure clásico](https://manage.windowsazure.com/), el inquilino aparece en **Todos los elementos** y también en **Active Directory**. Pueden usar a este inquilino como considere oportuno. También puede crear un nuevo inquilino con el menú **Nuevo** en la barra de tareas que aparece en la parte inferior.

#### No dispone de ninguna de las opciones anteriores y desea comenzar desde el principio
Si en su caso no se aplica ninguna de las opciones anteriores, vaya a [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para suscribirse a Azure con una organización nueva. Una vez suscrito, tendrá su propio inquilino de Azure AD con el nombre de dominio de su elección. En el [Portal de Azure clásico](https://manage.windowsazure.com/), puede ver el inquilino en **Active Directory** en el menú de la izquierda.

## Paso 2: Crear una suscripción de Azure nueva o utilizar una existente
Ahora que ya cuenta con un inquilino de AD, puede crear una suscripción de Azure nueva o utilizar una existente. La suscripción de Azure AD incluye varias ediciones. Para PowerApps Enterprise, puede usar la edición gratuita. Sin embargo, si necesita usar el proxy de AAD para crear una conectividad híbrida con los datos locales, debe usar la edición básica o premium.

Puede encontrar más características en las [ediciones de Azure Active Directory](../active-directory/active-directory-editions.md).


## Paso 3: Registrarse a PowerApps Enterprise en la suscripción profesional de Azure
> [AZURE.NOTE] Estos pasos requieren que el administrador de la suscripción inicie sesión en el Portal de Azure y envíe una solicitud.

Ahora que dispone de un inquilino de AD y una suscripción de Azure, los administradores de la suscripción profesional pueden registrarse en PowerApps Enterprise. El administrador también puede agregar a usuarios de la empresa para "administrar" PowerApps, lo que incluye otorgar permisos de usuario, y administren las instancias de PowerApps publicadas en la suscripción de Azure.

Si no se registra en PowerApps Enterprise, no verá ninguna hoja de acceso si va al [Portal de Azure](https://portal.azure.com/) y busca PowerApps. Para suscribir la empresa, el **administrador de suscripciones** puede ir a [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) y ponerse en contacto con nosotros para obtener más información sobre los precios y el proceso de suscripción.

![][4]

Una vez que finalice el proceso de suscripción y esté listo para PowerApps Enterprise, puede:

- Agregar a los usuarios de su empresa y, mediante el [control de acceso basado en rol](../role-based-access-control-configure.md), otorgar a estos usuarios roles de administrador para que obtengan acceso al portal de PowerApps Enterprise.
- Crear un entorno de servicio de aplicaciones dedicado para hospedar PowerApps.
- Crear API y conexiones para que se ejecuten dentro del entorno del Servicio de aplicaciones dedicado.
- Además de las aplicaciones creadas en PowerApps, puede agregar aplicaciones adicionales al entorno del Servicio de aplicaciones, incluidas aplicaciones web, aplicaciones móviles, aplicaciones de API y aplicaciones lógicas.

En el ejemplo siguiente, la empresa Contoso se suscribió a PowerApps. En esta nueva hoja de **PowerApps**, puede ver un resumen de los distintos tipos de aplicaciones creadas con este entorno del Servicio de aplicaciones. En **Administrar API**, puede ver un resumen de las API creadas por Microsoft (administradas por Microsoft) y ver las API creadas por Contoso (administradas por la TI):

![Ejemplo de la hoja de PowerApps para una empresa][3]


## Paso 4: Crear un entorno del Servicio de aplicaciones
Cree un entorno del Servicio de aplicaciones para hospedar las API y conexiones de PowerApps, así como también las aplicaciones móviles, aplicaciones web, aplicaciones de API y aplicaciones lógicas.

Un entorno del Servicio de aplicaciones es un entorno aislado y dedicado que ejecuta de manera segura todas las aplicaciones. Los recursos de proceso son por entorno del Servicio de aplicaciones y están dedicados exclusivamente a la ejecución de sus aplicaciones. Cuando se registra a PowerApps Enterprise, se usa un entorno del Servicio de aplicaciones dedicado para hospedar las API y conexiones que sus aplicaciones usan. Este entorno del Servicio de aplicaciones es un tipo "especial" de entorno del Servicio de aplicaciones. Concretamente:

- Puede usar este entorno del Servicio de aplicaciones para todo lo que quiera. Está vinculado a su empresa, no a la suscripción.
- Configura las API y conexiones para el uso por parte de las aplicaciones creadas en PowerApps. Sin embargo, también puede agregar aplicaciones web, aplicaciones móviles, aplicaciones lógicas y aplicaciones de API a este mismo entorno del Servicio de aplicaciones. 
- La facturación es fija y se incluye en PowerApps Enterprise.  
- Usted administra automáticamente la escala. No es necesario supervisar el entorno para determinar si se requieren recursos de proceso adicionales.

El entorno del Servicio de aplicaciones de Azure tiene características distintas. Consulte [Introducción al entorno del Servicio de aplicaciones](../app-service-app-service-environment-intro.md) para conocer estos detalles.

#### Requisitos de inicio

- Suscripción a Azure de la empresa
- El administrador de suscripciones de la empresa [suscribió la empresa a PowerApps](powerapps-get-started-azure-portal.md) Enterprise.
- Inició sesión en el Portal de Azure como el administrador de PowerApps ("propietario" de PowerApps) o el administrador de suscripciones.

### Crear un entorno del Servicio de aplicaciones
> [AZURE.NOTE] Si no ve la opción para crear el entorno del Servicio de aplicaciones, es porque ya está creado para su inquilino. Si desea ver los detalles, seleccione **Configuración** para abrir el entorno del Servicio de aplicaciones.

1. En el [Portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta profesional. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![Buscar PowerApps][1]
  
3. En la lista, puede desplazarse para encontrar PowerApps o escriba *powerapps*: ![Búsqueda de PowerApps][2]

4. En la hoja **PowerApps**, seleccione **Crear Entorno del Servicio de aplicaciones para comenzar** o **Entorno del Servicio de aplicaciones** en *Configuración*: ![][5]

	> [AZURE.NOTE] Si hace clic en **Crear Entorno del Servicio de aplicaciones para comenzar**, verá una hoja adicional con detalles sobre el Entorno del Servicio de aplicaciones. Solo haga clic en el vínculo Crear de esa hoja para iniciar la hoja de creación.

5. Luego, escriba el nombre, seleccione la suscripción que desea usar, seleccione un grupo de recursos o cree uno nuevo y seleccione una red virtual. **Tenga en cuenta** que después de elegir una red virtual, no puede cambiarla: ![][6] Para obtener más información sobre cómo funcionan las redes virtuales con un entorno del Servicio de aplicaciones, consulte [Creación de un entorno del Servicio de aplicaciones](../app-service-web-how-to-create-an-app-service-environment.md).

6. Seleccione **Agregar** para terminar de crear el entorno del Servicio de aplicaciones.

> [AZURE.TIP] Cuando crea el entorno del Servicio de aplicaciones con PowerApps, no se le solicita configurar grupos de recursos de proceso. Este paso se realiza de forma automática.

Recuerde que también puede agregar aplicaciones web, aplicaciones móviles y aplicaciones de API a este entorno del Servicio de aplicaciones. De hecho, es el entorno donde puede agregar todas las características que admite el Entorno del Servicio de aplicaciones.

### Agregar administradores para administrar el Entorno del Servicio de aplicaciones

Para obtener acceso al entorno del Servicio de aplicaciones, crear API, conexiones y otros recursos, los usuarios se deben agregar con el rol Propietario.

1. Seleccione el entorno del Servicio de aplicaciones que acaba de crear.
2. En Aspectos esenciales, seleccione la propiedad **Grupo de recursos**. Se abrirá el grupo de recursos que contiene el entorno del Servicio de aplicaciones: ![][7]
3. Seleccione el icono RBAC para administrar los permisos: ![][8] Agregar usuarios y asignar roles es como utilizar [Control de acceso basado en roles]( https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/) dentro de Azure.

> [AZURE.NOTE] Actualmente, no puede otorgar permisos de RBAC al entorno del Servicio de aplicaciones. Puede otorgar permisos de RBAC al nivel de grupo de recursos primario.

## Resumen y pasos siguientes
La empresa se suscribió a PowerApps y tiene un entorno del Servicio de aplicaciones. A continuación, puede agregar API y conexiones que sus aplicaciones pueden usar.

- [Supervisar las aplicaciones de PowerApps.](powerapps-manage-monitor-usage.md)
- [Desarrollar una API para PowerApps.](powerapps-develop-api.md)
- [Agregar una API nueva, una conexión y otorgar acceso a los usuarios.](powerapps-manage-api-connection-user-access.md)
- [Actualizar una API existente y sus propiedades.](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!---HONumber=AcomDC_0128_2016-->
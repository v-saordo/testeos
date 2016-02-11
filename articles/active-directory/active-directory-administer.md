<properties
	pageTitle="Administración del directorio de Azure AD | Microsoft Azure"
	description="Explica qué es un inquilino de Azure AD y cómo administrar Azure mediante Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	writer="markvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/25/2016"
	ms.author="markvi"/>

# Administración del directorio de Azure AD

## ¿Qué es un inquilino de Azure AD?

En un área de trabajo física, la palabra "inquilino" puede definirse como un grupo o una compañía que ocupa un edificio. Por ejemplo, es posible que su organización disponga de un espacio de oficinas en un edificio. Puede que este edificio se encuentre en una calle con varias organizaciones más. En este caso, su organización se consideraría inquilina de dicho edificio. Este edificio es un activo de su organización; proporciona seguridad y garantiza que pueda desarrollar sin problemas las actividades propias de su negocio. También está separado de otras empresas en la misma calle. Esto garantiza que su organización y los activos que contiene están aislados de otras organizaciones.

En un área de trabajo habilitada en la nube, un inquilino puede definirse como un cliente o una organización que posee y administra una instancia específica de ese servicio en la nube. Con la plataforma de identidad proporcionada por Microsoft Azure, un inquilino es simplemente una instancia dedicada de Azure Active Directory (Azure AD) que su organización recibe y posee cuando se suscribe a un servicio en la nube de Microsoft, como Azure u Office 365.

Cada directorio de Azure AD es distinto e independiente de otros directorios de Azure AD. Del mismo modo que un edificio de oficinas para empresas es un activo seguro dedicado específicamente a su organización, un directorio de Azure AD se ha diseñado también para ser un activo seguro para el uso exclusivo de su organización. La arquitectura de Azure AD aísla los datos del cliente y la información de identidad para evitar contactos cruzados. Esto significa que los usuarios y los administradores de un directorio de Azure AD no tendrán acceso a los datos de otro directorio, ya sea de manera involuntaria o malintencionada.

![Administración de Azure Active Directory][1]

## ¿Cómo puedo obtener un directorio de Azure AD?

Azure AD proporciona las principales capacidades de administración de identidades y directorios de la mayoría de los servicios en la nube de Microsoft, incluyendo:

- Las tablas de Azure
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

Al suscribirse en cualquiera de estos servicios en la nube de Microsoft, obtendrá un directorio de Azure AD. Puede crear directorios adicionales según sea necesario. Por ejemplo, puede mantener el primer directorio como directorio de producción y, a continuación, crear otro directorio para pruebas o ensayos.

> [AZURE.NOTE]
Cuando se haya suscrito a un primer servicio, se recomienda que utilice la misma cuenta de administrador asociada con su organización si se suscribe a otros servicios en la nube de Microsoft.

La primera vez que se suscriba a un servicio en la nube de Microsoft, se le solicitará que proporcione detalles acerca de su organización y el registro de nombres de dominio en Internet de su organización. Esta información se utilizará a continuación para crear una nueva instancia de directorio de Azure AD para su organización. Ese mismo directorio se usará para autenticar los intentos de inicio de sesión cuando se suscriba a varios servicios en la nube de Microsoft.

Los servicios adicionales aprovechan al máximo toda las cuentas de usuario, directivas, configuraciones o integraciones de directorios locales existentes que configure para ayudar a mejorar la eficacia entre la infraestructura de identidades local de su organización y Azure AD.

Por ejemplo, si se suscribió originalmente a Microsoft Intune y completó los pasos necesarios para seguir integrando su Active Directory local con su directorio Azure AD mediante la implementación de la sincronización de directorios o servidores de inicio de sesión único, puede suscribirse a otro servicio en la nube de Microsoft, como Office 365, que también puede aprovechar los mismos beneficios de integración de directorios que ahora utiliza con Microsoft Intune.

Para obtener más información acerca de cómo integrar su directorio local con Azure AD, vea [Integración de directorios](active-directory-aadconnect.md).

### Asociación de un directorio de Azure AD con una nueva suscripción de Azure

Puede asociar una nueva suscripción de Azure con el mismo directorio que autentica el inicio de sesión para una suscripción de Office 365 o Microsoft Intune existente. Inicie sesión en el Portal de administración de Azure con la cuenta profesional o educativa. El Portal de administración devuelve un mensaje informando que no pudo encontrar ninguna suscripción para esa cuenta. Seleccione **Suscribirse a Azure**, y el directorio estará disponible para la administración dentro del portal. Para obtener más información, consulte [Administrar el directorio de su suscripción de Office 365 en Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Para ver un vídeo sobre preguntas comunes relacionadas con el uso de Azure AD, consulte [Azure Active Directory: preguntas comunes acerca del registro, el inicio de sesión y el uso.](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### Creación de un directorio de Azure AD mediante una suscripción a un servicio en la nube de Microsoft como una organización

Si aún no tiene una suscripción a un servicio en la nube de Microsoft, utilice uno de los siguientes vínculos para suscribirse. Con la suscripción a su primer servicio se creará un directorio de Azure AD automáticamente.

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### Administración de un directorio de aprovisionamiento predeterminado de Azure

Actualmente, al suscribirse a Azure, se crea automáticamente un directorio y la suscripción queda asociada a ese directorio. No obstante, si se suscribió por primera vez a Azure antes de octubre de 2013, el directorio no se habrá creado automáticamente. En ese caso, Azure puede haber "repuesto" su cuenta mediante el aprovisionamiento de un directorio predeterminado para ella. Su suscripción se asoció entonces con ese directorio predeterminado.

La reposición de directorios tuvo lugar en octubre de 2013 como parte de una mejora general del modelo de seguridad de Azure. Ayuda a ofrecer características de identidad organizativa a todos los clientes de Azure y garantiza que el acceso a todos los recursos de Azure se produce en el contexto de un usuario en un directorio. No es posible usar Azure sin un directorio. Para poder hacerlo, los usuarios que se hubieran registrado antes del 7 de julio de 2013 y no tuvieran un directorio, tenían que tener uno creado. Si ya tenía un directorio creado, su suscripción se asoció con ese directorio.

No hay costes asociados al uso de Azure AD. El directorio es un recurso gratuito. Existe un nivel adicional Azure Active Directory Premium que se ofrece como licencia separada y proporciona características adicionales, como la información de marca de empresa y el autoservicio de restablecimiento de contraseñas.

Para cambiar el nombre para mostrar del directorio, haga clic en el directorio del portal y luego en **Configurar**. Como se explica más adelante en este tema, puede agregar un nuevo directorio o eliminar uno que ya no necesite. Para asociar su suscripción a un directorio diferente, haga clic en la extensión **Configuración** del panel izquierdo y, en la parte inferior de la página **Suscripciones**, haga clic en **Editar directorio**. También puede crear un dominio personalizado mediante un nombre DNS que haya registrado en lugar del predeterminado *.onmicrosoft.com, que puede ser preferible para un servicio como SharePoint Online.

## ¿Cómo puedo administrar datos de directorio?

Como administrador de una o más suscripciones de servicio en la nube de Microsoft, puede usar el Portal de administración de Azure, el portal de cuentas de Microsoft Intune o el centro de administración de Office 365 para administrar datos de directorio de su organización. También puede descargar y ejecutar los cmdlets del [módulo de Microsoft Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx), que le ayudarán a administrar los datos almacenados en Azure AD.

En cualquiera de estos portales (o cmdlets), puede:

- Crear y administrar cuentas de usuario y grupo
- Administrar servicios en la nube relacionados a los que se suscribe su organización
- Configurar la integración local con el servicio de directorio

El portal de administración de Azure, el centro de administración de Office 365, el portal de cuentas de Microsoft Intune y los cmdlets de Azure AD leen y escriben en una instancia compartida única de Azure AD que está asociada con el directorio de su organización, tal como se muestra en la siguiente ilustración. De esta manera, los portales (o cmdlets) actúan como interfaz front-end que extraen o modifican los datos de directorio.

![][2]

Estos portales de cuentas y los cmdlets asociados de PowerShell de Azure AD que se utilizan para administrar usuarios y grupos se crean sobre la plataforma de Azure AD.

Al realizar un cambio en los datos de su organización mediante cualquiera de los portales (o cmdlets) manteniendo la sesión iniciada en el contexto de uno de estos servicios, el cambio se mostrará también en los demás portales la próxima vez que inicie sesión en el contexto de dicho servicio, ya que estos datos se comparten entre los servicios en la nube de Microsoft a los que esté suscrito. Por ejemplo, si utiliza el centro de administración de Office 365 para bloquear el inicio de sesión de un usuario, dicha acción bloqueará el inicio de sesión de ese usuario en cualquier otro servicio al que esté suscrita actualmente su organización. Si fuera a usar esa misma cuenta de usuario en el contexto del portal de cuentas de Microsoft Intune, vería que el usuario está bloqueado.

## ¿Cómo puedo agregar y administrar varios directorios?

Puede agregar un directorio de Azure AD en el Portal de administración de Azure. Seleccione la extensión **Active Directory** a la izquierda y haga clic en **Agregar**.

Puede administrar cada directorio como un recurso totalmente independiente: cada directorio es un par con características completas y lógicamente independiente de otros directorios que administre; no existe ninguna relación principal-secundario entre los directorios. Esta independencia entre directorios incluye la independencia de recursos, la independencia administrativa y la independencia de sincronización.

- **Independencia de recursos**. Si crea o elimina un recurso en un directorio, esto no tiene efecto en ningún recurso de los demás directorios, con la excepción parcial de los usuarios externos, según se describe más adelante. Si utiliza un dominio personalizado “contoso.com” con un directorio, este no se puede utilizar con ningún otro directorio.
- **Independencia administrativa**. Si un usuario no administrador del directorio "Contoso", crea un directorio de prueba "Prueba". En ese caso:
    - ◦ La herramienta de sincronización de directorios, para sincronizar datos con un único bosque de AD.
    - ◦ Los administradores del directorio "Contoso" no tienen privilegios administrativos directos en el directorio "Prueba", a menos que el administrador de "Prueba" les otorgue estos privilegios específicamente. Los administradores de "Contoso" pueden controlar el acceso al directorio "Prueba" en virtud del control que tengan de la cuenta de usuario que creó "Prueba".

    Y si cambia (agrega o quita) un rol de administrador de un usuario en un directorio, el cambio no afecta a ningún rol de administrador que ese usuario pueda tener en otro directorio.


- **Independencia de sincronización**. Puede configurar cada Azure AD de manera independiente para que los datos se sincronicen desde una sola instancia de:
    - La herramienta de sincronización de directorios, para sincronizar los datos con un único bosque de AD.
    - El conector de Azure Active Directory para Forefront Identity Manager, para sincronizar datos con uno o varios bosques locales u orígenes de datos distintos de AD.

También tenga en cuenta que, a diferencia de otros recursos de Azure, los directorios no son recursos secundarios de una suscripción a Azure. Así, si cancela su suscripción a Azure o deja que esta caduque, aún podrá tener acceso a los datos de su directorio mediante Azure AD PowerShell, la API Graph de Azure u otras interfaces, como el Centro de administración de Office 365. También puede asociar otra suscripción con el directorio.

## ¿Cómo puedo eliminar un directorio de Azure AD?
Un administrador global puede eliminar un directorio de Azure AD desde el portal. Antes de eliminar un directorio, asegúrese de que realmente no lo necesita, ya que también se eliminarán todos los recursos que contiene.

> [AZURE.NOTE]
Si el usuario inicia sesión con una cuenta profesional o educativa, el usuario no debe intentar eliminar su directorio particular. Por ejemplo, si el usuario ha iniciado sesión como joe@contoso.onmicrosoft.com, ese usuario no puede eliminar el directorio que tiene contoso.onmicrosoft.com como dominio predeterminado.

### Condiciones que deben cumplirse para eliminar un directorio de Azure AD

Azure AD requiere que se cumplan determinadas condiciones para eliminar un directorio. Esto reduce el riesgo de que la eliminación de un directorio afecte negativamente a usuarios o aplicaciones, como la capacidad de los usuarios de iniciar sesión en Office 365 o de tener acceso a recursos en Azure. Por ejemplo, si se elimina de forma involuntaria un directorio de una suscripción, los usuarios no podrían tener acceso a los recursos de Azure de esa suscripción.

Se comprueban las condiciones siguientes:

- El único usuario en el directorio es el administrador global que eliminará el directorio. Se deben eliminar los otros usuarios antes de poder eliminar el directorio. Si los usuarios se sincronizan localmente, se deberá desactivar la sincronización y se deberá eliminar a los usuarios en el directorio en la nube mediante el Portal de administración o el módulo de Azure para Windows PowerShell. No existen requisitos para eliminar grupos o contactos, como los contactos agregados desde el centro de administración de Office 365.
- No puede haber aplicaciones en el directorio. Las aplicaciones se deben eliminar antes de poder eliminar el directorio.
- No puede haber suscripciones a ningún servicio de Microsoft Online Services, como Microsoft Azure, Office 365 o Azure AD Premium, asociadas al directorio. Por ejemplo, si se ha creado un directorio predeterminado en Azure en su nombre, no puede eliminar este directorio si la suscripción a Azure aún se basa en este directorio para la autenticación. De igual forma, no puede eliminar un directorio si otro usuario le ha asociado una suscripción. Para asociar su suscripción a un directorio diferente, inicie sesión en el Portal de administración de Azure y haga clic en **Configuración** en el panel izquierdo. A continuación, en la parte inferior de la página **Suscripciones**, haga clic en **Editar directorio**. Para obtener más información acerca de las suscripciones de Azure, consulte [Cómo se asocian las suscripciones a Azure con Azure AD](active-directory-how-subscriptions-associated-directory.md).

    > [AZURE.NOTE]
    Si el usuario inicia sesión con una cuenta profesional o educativa, el usuario no debe intentar eliminar su directorio particular. Por ejemplo, si el usuario ha iniciado sesión como joe@contoso.onmicrosoft.com, ese usuario no puede eliminar el directorio que tiene contoso.onmicrosoft.com como dominio predeterminado.

- No se pueden vincular proveedores de Multi-Factor Authentication al directorio.


## Recursos adicionales

- [Foro de Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Foro de Multi-Factor Authentication de Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Administración de Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

<!---HONumber=AcomDC_0128_2016-->
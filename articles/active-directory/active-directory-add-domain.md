<properties
	pageTitle="Incorporación de su propio nombre de dominio a Azure Active Directory | Microsoft Azure"
	description="Se explica cómo agregar su propio nombre de dominio a Azure Active Directory y otra información relacionada."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/05/2016"
	ms.author="curtand"/>

# Incorporación de su propio nombre de dominio a Azure Active Directory

Cuando se registre en un servicio en la nube de Microsoft, se le facilitará un nombre de dominio que tiene el siguiente formato: contoso.onmicrosoft.com. Puede seguir utilizando ese nombre de dominio inicial o bien agregar su propio nombre de dominio personalizado al servicio en la nube. En este tema se explica cómo agregar su propio nombre de dominio y la información relacionada con Azure Active Directory (Azure AD).

Es posible que a los usuarios de Office 365 también le interesen los siguientes temas relacionados:

- [Conceptos básicos de DNS](https://support.office.com/article/854b6b2b-0255-4089-8019-b765cff70377)
- [Encuentre su registrador de dominio o proveedor de host DNS ](https://support.office.com/article/Find-your-domain-registrar-or-DNS-hosting-provider-b5b633ba-1e56-4a98-8ff5-2acaac63a5c8/)
- [Trabajar con nombres de dominio en Office 365](https://support.office.com/article/Work-with-domain-names-in-Office-365-4f1bd681-337a-4bd3-a7b7-cf77b18d0870/)

## Acerca de su dominio onmicrosoft.com

Puede utilizar el dominio onmicrosoft.com con otros servicios. Por ejemplo, puede usar el dominio con Exchange Online y Lync Online para crear listas de distribución y cuentas de inicio de sesión para que los usuarios puedan tener acceso a SharePoint Online y las colecciones de sitios.

Si agrega sus propios nombres de dominio a su directorio, puede seguir utilizando el dominio onmicrosoft.com.

Una vez elegido el nombre que se va a utilizar con el servicio en la nube durante la suscripción, como contoso.onmicrosoft.com, no podrá cambiarlo.

## ¿Cómo puedo agregar mi propio dominio?

Si su organización ya tiene un nombre de dominio personalizado, como administrador, puede agregarlo a su directorio de Azure AD para utilizarlo con todos los servicios en línea de Microsoft a los que se haya suscrito. Una vez agregado su nombre de dominio a Azure AD, puede empezar a asociar dicho nombre de dominio a los distintos servicios en la nube.

Puede agregar hasta 900 nombres de dominio a su directorio de Azure AD mediante el uso de una de estas opciones:

- El Portal de Azure clásico, el portal de Office 365 o el portal de Microsoft Intune.
- El módulo de Azure Active Directory para Windows PowerShell. Para obtener más información sobre el cmdlet que puede usar para ello, vea [Administración de dominios en Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx).

Ya debe haber registrado un nombre de dominio y tener las credenciales de inicio de sesión necesarias para el registrador de nombres de dominio (por ejemplo, GoDaddy o Register.com).

Puede agregar varios dominios a su directorio. Sin embargo, no puede agregar el mismo dominio a directorios distintos. Así, por ejemplo, si agrega el dominio a su directorio, no puede crear otro directorio de Azure AD y agregarle el mismo nombre de dominio.

Si piensa utilizar el inicio de sesión único con el servicio en la nube, se recomienda que ayude a preparar el entorno de Active Directory mediante la ejecución de la herramienta de preparación de la implementación de Microsoft. Esta herramienta inspecciona el entorno de Active Directory y proporciona un informe que incluye información sobre si está listo para configurar el inicio de sesión único. Si no lo está, esta herramienta muestra los cambios que necesita hacer para prepararse para el inicio de sesión único. Por ejemplo, inspecciona si los usuarios tienen UPN y si esos UPN tienen el formato correcto. Para descargar la herramienta, consulte [Herramienta de preparación de implementación de Microsoft](http://go.microsoft.com/fwlink/?linkid=235650).

> [AZURE.NOTE]¿Utiliza Office 365? Una vez que haya configurado su dominio, puede empezar a crear direcciones de correo electrónico, cuentas de Lync Online y grupos de distribución que usen su nombre de dominio personalizado. También puede utilizar el nombre de dominio para un sitio web de acceso público hospedado en SharePoint Online.

- [Incorporación y comprobación de un dominio mediante el Portal de Azure clásico](#add-and-verify-a-domain-using-the-azure-management-portal)
- [Editar registros DNS para servicios en la nube](#edit-dns-records-for-your-cloud-services)
- [Comprobar un dominio en cualquier registrador de nombres de dominio](#verify-a-domain-at-any-domain-name-registrar)

### Incorporación y comprobación de un dominio mediante el Portal de Azure clásico

1. En el Portal de Azure clásico, haga clic en **Active Directory** y luego haga clic en el nombre del directorio de su organización. Puede realizar una de las acciones siguientes:
    1. En la página del directorio predeterminado, haga clic en **Agregar dominio** en la sección **Mejorar la experiencia de inicio de sesión del usuario**.
2. Haga clic en **Dominios** y, a continuación, haga clic en **Agregar un dominio de cliente** o en el botón **Agregar**.
2. En la página **Agregar dominio**, escriba el nombre de dominio que desea agregar y realice una de las acciones siguientes:
    1. Si no tiene previsto integrar Active Directory local con Azure AD, haga lo siguiente:
        1. Deje la casilla **Pretendo configurar este dominio para el inicio de sesión único con mi Active Directory local** desactivada y haga clic en el botón **Agregar**.
        2. Una vez que aparezca el mensaje que informa de que el dominio se ha agregado correctamente a Azure AD, haga clic en la flecha para desplazarse a la página siguiente a fin de comprobar su dominio.
        3. Siga las instrucciones en la página siguiente para comprobar que el nombre de dominio que agregó en los pasos anteriores le pertenece. Para obtener instrucciones detalladas, consulte Comprobar un dominio en cualquier registrador de nombres de dominio.
    2. Si desea integrar su Active Directory local con Azure AD, haga lo siguiente:
        1. Asegúrese de activar la casilla **Pretendo configurar este dominio para el inicio de sesión único con mi Active Directory local** y luego haga clic en el botón **Agregar**.
        2. Una vez que aparezca el mensaje que informa de que el dominio se ha agregado correctamente a Azure AD, haga clic en la flecha para desplazarse a la página siguiente y, a continuación, siga las instrucciones de esa página para configurar el dominio agregado para el inicio de sesión único.

> [AZURE.NOTE]Después de agregar el nombre de dominio a Azure AD, puede cambiar el nombre de dominio predeterminado para nuevas direcciones de correo electrónico. Para obtener más información, consulte [¿Cómo puedo cambiar el nombre de dominio principal para los usuarios?](#how-can-i-change-the-primary-domain-name-for-users?) También puede editar el perfil de una cuenta de usuario existente para actualizar la dirección de correo electrónico (que también es el identificador de usuario) a fin de utilizar el nombre de dominio personalizado en lugar del dominio onmicrosoft.com.

### Edición de registros DNS para servicios en la nube

> [AZURE.NOTE]¿Usa Microsoft Intune? No es necesario editar los registros de DNS para el servicio en la nube de Microsoft Intune.

Después de agregar y comprobar su nombre de dominio personalizado, el paso siguiente es modificar los registros DNS en el registrador de dominios o el proveedor de host DNS que dirige el tráfico al servicio en la nube. Azure AD proporciona la información de DNS que necesita.

Si ha completado el asistente para **agregar un dominio**, haga clic en **Configurar registros de DNS**. De lo contrario, siga estos pasos.

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en **Dominios**.
2. En función del portal que utilice, haga clic en el nombre de dominio que desea configurar y, a continuación, haga clic en **Configuración DNS** o **Ver la configuración DNS**. En la página **Configuración DNS** se enumeran los registros DNS del servicio en la nube.

    Si quiere configurar un servicio que no aparece en la pestaña Configuración DNS, compruebe las selecciones de servicios de dominio para asegurarse de que eligió ese servicio para este nombre de dominio. Para cambiar la configuración, por ejemplo, para agregar Lync Online, vea Especificar los servicios que utilizará con su dominio.

3. En el sitio web registrador del nombre de dominio, agregue los registros necesarios al archivo DNS.

Los cambios suelen tardar unos 15 minutos en surtir efecto. Sin embargo, el registro DNS que ha creado puede tardar hasta 72 horas en propagarse por el sistema DNS. Si necesita ver la configuración de estos registros de nuevo, en la página **Dominios**, haga clic en el dominio y, a continuación, en la página **Propiedades del dominio**, haga clic en la pestaña **Configuración DNS**.

Para comprobar el estado de su dominio, en la página **Dominios**, haga clic en el dominio y, a continuación, en la página **Propiedades del dominio**, haga clic en **Solucionar problemas de dominio**.

### Comprobación de un dominio en cualquier registrador de nombres de dominio

Si ya tiene un dominio registrado con un registrador de nombres de dominio y desea configurarlo de modo que funcione con Azure AD, es necesario comprobar el dominio para confirmar que usted es el propietario del mismo. Para comprobar el dominio, se crea un registro DNS en el registrador de nombres de dominio, o donde se hospede su DNS y, a continuación, Azure AD usa ese registro para confirmar que usted es el propietario del dominio.

Antes de poder comprobar su dominio, debe agregar un dominio personalizado a Azure AD. Si se ha agregado un dominio personalizado pero este aún no se ha comprobado, el estado se mostrará como **Haga clic para comprobar el dominio** o **No comprobado**.

#### Recopilación de la información de dominio

Según el portal que utilice para administrar el directorio de Azure AD, debe recopilar cierta información sobre el dominio para que posteriormente pueda crear un registro DNS que se usará durante el proceso de comprobación.

Si está usando Microsoft Intune o el portal de la cuenta de Azure:

1. En la página **Dominios**, en la lista de nombres de dominio, busque el dominio que esté verificando. En la columna **Estado**, haga clic en **Haga clic para comprobar el dominio**.
2. En la página **Comprobar dominio**, en la lista desplegable **Consulte las instrucciones para llevar a cabo este paso con:**, elija el proveedor de host DNS. Si el proveedor no aparece en la lista, elija **Instrucciones generales**.
3. En la lista desplegable **Seleccione un método de verificación:**, elija **Agregar un registro TXT (método preferido)** o **Agregar un registro MX (método alternativo)**.

    Si el proveedor de host DNS le permite crear registros TXT, se recomienda que use un registro TXT para la comprobación. ¿Por qué? Los registros TXT son fáciles de crear y no existe la posibilidad de interferencia con la entrega de correo electrónico si se especifica involuntariamente un valor incorrecto.

4. En la tabla, copie o registre la información de **Dirección de destino**.

Si está usando el Portal de Azure clásico:

1. En el Portal de Azure clásico, haga clic en **Active Directory**, haga clic en el nombre del directorio y luego haga clic en **Dominios**.
2. En la página **Dominios**, en la lista de nombres de dominio, haga clic en el dominio que desee comprobar y, a continuación, haga clic en **Comprobar**.
2. En la página **Comprobar**, en la lista desplegable **Tipo de registro**, elija **Registro TXT** o **Registro MX**.
3. Copie o anote la información que aparece debajo.

#### Incorporación de un registro DNS en el registrador de nombres de dominio

Azure AD usa un registro DNS que usted crea en el registrador de nombres de dominio para confirmar que usted es el propietario del dominio. Utilice las instrucciones siguientes para crear un tipo de registro TXT o MX para un dominio que está registrado en su registrador.

Si el registrador de dominios no acepta "@" como nombre de host, póngase en contacto con el registrador de dominios para averiguar cómo representar el "elemento principal de la zona actual".

Office 365 tiene [instrucciones específicas para los registradores de dominio comunes](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

Para obtener instrucciones generales, siga estos pasos para agregar un registro TXT o MX:

1. Inicie sesión en el sitio web del registrador de su nombre de dominio y, a continuación, seleccione el dominio que quiera comprobar.
2. En el área de administración de DNS de su cuenta, seleccione la opción para agregar un registro TXT o MX para el dominio.
3. En el cuadro **TXT** o **MX** del dominio, escriba lo siguiente: @
4. En el cuadro **Nombre de dominio completo (FQDN)** o **Apunta a**, escriba o pegue la **Dirección de destino** que anotó en el paso anterior.
5. En el caso de un registro TXT, se solicitará información **TTL**, escriba **1** para establecer el período de vida en una hora.

    En el caso de un registro MX, se solicitará un tipo de prioridad (o preferencia), escriba un número que sea mayor que el número especificado para los registros MX existentes. Esto puede ayudar a impedir que el nuevo registro MX interfiera con el enrutamiento de correo para el dominio. En lugar de una prioridad, verá las siguientes opciones: **Bajo**, **Medio**, **Alto**. En este caso, elija **Bajo**.

6. Guarde los cambios y, a continuación, cierre sesión en el sitio web del registrador de su nombre de dominio.

Después de crear el registro TXT o el registro MX y cerrar sesión en el sitio web, vuelva al servicio en la nube para comprobar el dominio. Los cambios suelen tardar unos 15 minutos en surtir efecto. Sin embargo, el registro que ha creado puede tardar hasta 72 horas en propagarse por el sistema DNS.

#### Verificación de su dominio

Después de que el registro que ha creado para el dominio se haya propagado correctamente por el sistema DNS, haga lo siguiente para acabar de comprobar su dominio con Azure AD.

1. En Azure Active Directory en el Portal de Azure clásico, haga clic en **Dominios**.
2. En la lista **Dominios**, busque el dominio que se va a comprobar y, a continuación, según el portal que utilice, haga clic en **Haga clic para comprobar el dominio** o en **Comprobar**.
3. Siga las instrucciones para completar el proceso de comprobación.
    - Si la comprobación del dominio se realiza correctamente, se le notificará que el dominio se ha agregado a su cuenta.
    - Si se produce un error en la comprobación del dominio, los cambios que realizó en el registrador de dominios tardarán más tiempo en propagarse. Cancele el proceso de comprobación y vuelva más tarde para intentar de nuevo la comprobación.

Si han pasado más de 72 horas desde que hizo los cambios en su dominio, inicie sesión en el sitio web del registrador de dominios y compruebe que especificó correctamente la información del alias. Si escribió incorrectamente la información, debe quitar el registro DNS incorrecto y crear uno nuevo con la información correcta utilizando los procedimientos de este tema.

Una vez comprobado el dominio, puede configurarlo para que funcione con sus cuentas.

## ¿Cómo puedo cambiar el nombre de dominio principal para los usuarios?

Después de agregar el nombre de dominio a Azure AD, puede cambiar el nombre de dominio que debería mostrarse como el valor predeterminado cuando se crea una nueva cuenta de usuario. Para ello, siga estos pasos.

1. En el Portal de Azure clásico, en la esquina superior izquierda, haga clic en el nombre de su organización.
2. Haga clic en **Editar**.
3. Elija un nuevo nombre de dominio predeterminado, como el nombre de dominio personalizado que ha agregado.

## ¿Cómo puedo quitar un dominio?

Antes de quitar un nombre de dominio, se recomienda que lea la siguiente información:

- No se puede quitar el nombre de dominio contoso.onmicrosoft.com original que se proporcionó para el directorio al suscribirse.
- Un dominio de nivel superior que tenga subdominios asociados no se podrá quitar hasta que se hayan quitado dichos subdominios. Por ejemplo, no puede quitar adatum.com si tiene corp.adatum.com u otro subdominio que use el nombre de dominio de nivel superior. Para obtener más información, consulte este [artículo de soporte técnico](https://support.microsoft.com/kb/2787792/).
- ¿Ha activado la sincronización de directorios? Si es así, se habrá agregado automáticamente a su cuenta un dominio con un aspecto similar a este: contoso.mail.onmicrosoft.com. No se puede quitar este nombre de dominio.
- Para poder quitar un nombre de dominio, primero debe quitar el nombre de dominio de todas las cuentas de usuario o correo electrónico asociadas con el dominio. Puede quitar todas las cuentas o bien editarlas en masa para cambiar las direcciones de correo electrónico y la información de nombre de dominio. Para obtener más información, consulte [Creación o edición de usuarios en Azure AD](active-directory-create-users.md).
- Si hospeda un sitio de SharePoint Online en un nombre de dominio que se utiliza para una colección de sitios de SharePoint Online, debe eliminar la colección de sitios para poder quitar el nombre de dominio.

Para quitar un nombre de dominio:

1. En Azure AD en el Portal de Azure clásico, en el panel izquierdo, haga clic en **Dominios**.
2. En la página **Dominios** seleccione el nombre de dominio que desea quitar y, a continuación, haga clic en **Quitar dominio**.
3. En la página **Quitar dominio**, haga clic en **Sí**.

Si no se puede quitar el nombre de dominio en este momento, el estado del nombre de dominio aparece como Eliminación en espera en la página Dominios. Si continúa viendo este estado, intente quitar el nombre de dominio de nuevo.

## Solución de problemas después de cambiar el nombre de dominio

### He realizado cambios en mi dominio, pero este no muestra aún los cambios.

Debido a la manera en que las actualizaciones se desplazan a través del sistema de nombres de dominio (DNS), pueden transcurrir hasta 72 horas para que los cambios realizados en el registrador de dominios o en el proveedor de host se propaguen completamente por Internet y pueda empezar a utilizar el nombre de dominio con los servicios.

Además, las modificaciones que realice en el registrador de dominios deben ser totalmente correctas. Si vuelve para corregir un error, es posible que trascurran varios días hasta que el valor actualizado aparezca en el sitio del portal del servicio en la nube.

¿Cuánto tardará? Depende en parte del valor del período de vida (TTL) que haya especificado para el registro DNS que está sustituyendo o actualizando. Hasta que no expire el período de vida, los servidores de Internet que hayan almacenado en caché los datos anteriores no consultarán el servidor de nombres autoritativo para solicitar el nuevo valor.

### He agregado un dominio, lo he comprobado y he configurado los registros DNS en el sitio del registrador de dominios. ¿Por qué las nuevas cuentas de correo electrónico no reciben correo todavía?

Una vez que haya terminado de agregar o actualizar los registros DNS de su dominio, pueden transcurrir hasta 72 horas para que los cambios surtan efecto.

Además, la información de configuración debe ser totalmente correcta en el sitio del registrador de dominios. Compruebe de nuevo la configuración y asegúrese de que ha asignado tiempo suficiente para que los registros DNS modificados se propaguen por el sistema.

### No se puede comprobar el nombre de dominio. ¿Cómo puedo saber qué ha pasado?

Una manera de realizar un seguimiento de los problemas es utilizar el asistente para la solución de problemas de dominios. Para iniciar el asistente, haga lo siguiente: en el Portal de Azure clásico, en la página de administración, haga clic en **Dominios** y luego haga doble clic en el nombre de dominio que desea comprobar. A continuación, en **Solución de problemas**, haga clic en **Solucionar problemas de dominio**.

El asistente para la solución de problemas le solicita información acerca del punto del proceso de verificación en que se encuentra y, a continuación, le ofrece información para ayudarle a completar la comprobación.

### He agregado y comprobado mi dominio, pero el nuevo nombre de dominio no funciona para las direcciones de correo electrónico de los usuarios existentes.

Si agrega el nombre de dominio personalizado al servicio en la nube después de haber agregado cuentas de usuario, es posible que tenga que aplicar actualizaciones para usar el nuevo nombre de dominio. Por ejemplo, necesitará editar las cuentas de los usuarios para configurar sus direcciones de correo electrónico a fin de utilizar el dominio personalizado.

## Pasos siguientes

- [Foro de Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Administración de dominios en Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx)

<!---HONumber=AcomDC_0107_2016-->
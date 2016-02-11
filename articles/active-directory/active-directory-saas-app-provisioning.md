<properties
   pageTitle="Aprovisionamiento automático de usuarios para aplicaciones SaaS | Microsoft Azure"
   description="Una introducción sobre cómo puede usar Azure AD para el aprovisionamiento, el desaprovisionamiento y la actualización continua de cuentas de usuario de manera automática en varias aplicaciones SaaS de terceros."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="liviodlc"/>

#Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory

##¿Qué es el aprovisionamiento automático de usuarios para aplicaciones SaaS?

Azure Active Directory (Azure AD) permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones de nube (SaaS) como Dropbox, Salesforce, ServiceNow y muchas más.

**A continuación se muestran algunos ejemplos de lo que esta característica permite hacer:**

- Crear automáticamente nuevas cuentas en las aplicaciones SaaS adecuadas para los usuarios nuevos cuando se unen a su equipo.
- Desactivar cuentas automáticamente de las aplicaciones SaaS cuando los usuarios se vean obligados a abandonar el equipo.
- Garantizar que las identidades se mantienen actualizadas en las aplicaciones SaaS según los cambios en el directorio.
- Aprovisionar objetos que no son de usuario, como grupos, para aplicaciones SaaS que los admiten.

**El aprovisionamiento automático de usuarios también incluye la funcionalidad siguiente:**

- La capacidad de hacer coincidir las identidades existentes entre Azure AD y las aplicaciones SaaS.
- Opciones de personalización para ayudar a Azure AD a ajustar las configuraciones actuales de las aplicaciones SaaS que su organización utiliza actualmente.
- Alertas de correo electrónico opcionales para errores de aprovisionamiento.
- Informes y registros de actividades para facilitar la supervisión y solución de problemas.

##¿Por qué usar el aprovisionamiento automático?

Las razones habituales por las que se debe usar esta característica son:

- Evitar los costes, las ineficiencias y los errores humanos asociados con los procesos de aprovisionamiento manuales.
- Proteger su organización mediante la eliminación instantánea de las identidades de los usuarios de aplicaciones SaaS claves cuando estos abandonan la organización.
- Importar una cantidad masiva de usuarios fácilmente a una aplicación SaaS concreta.
- Disfrutar de la comodidad de que la solución de aprovisionamiento se ejecute fuera de las mismas directivas de acceso a las aplicaciones definidas para el Inicio de sesión único de Azure AD.

##Preguntas frecuentes

**¿Con qué frecuencia Azure AD escribe los cambios de directorio en las aplicaciones SaaS?**

Azure AD comprueba si hay cambios a intervalos de cinco a diez minutos. Si la aplicación SaaS devuelve varios errores (como en el caso de las credenciales de administrador no válidas), Azure AD disminuye gradualmente su frecuencia hasta una vez al día hasta que se solucionan los errores.

**¿Cuánto tiempo se tarda en aprovisionar usuarios?**

Los cambios incrementales se producirán casi instantáneamente, pero si intenta aprovisionar la mayor parte de su directorio, entonces depende del número de usuarios y grupos que tiene. Los directorios pequeños tardan sólo unos minutos, los medianos pueden tardar varios minutos y los directorios muy grandes pueden tardar varias horas.

**¿Cómo se puede seguir el progreso del trabajo de aprovisionamiento actual?**

Puede revisar el informe de aprovisionamiento de cuentas en la sección Informes de su directorio. Otra opción es visitar la pestaña Panel de la aplicación SaaS que se va a aprovisionar y buscar en la sección "Estado de integración" cerca de la parte inferior de la página.

**¿Cómo puedo saber si se produce algún error en el aprovisionamiento de un usuario?**

Al final del asistente de configuración del aprovisionamiento hay una opción para suscribirse a notificaciones de correo electrónico para errores de aprovisionamiento. También puede comprobar el informe de errores de aprovisionamiento para saber qué usuarios no se han podido aprovisionar y por qué.

**¿Azure AD puede escribir cambios desde la aplicación SaaS en el directorio?**

Para la mayoría de las aplicaciones SaaS, el aprovisionamiento es sólo de salida, lo que significa que los usuarios se escriben en la aplicación desde el directorio, y los cambios de la aplicación no se pueden volver a escribir en el directorio. Para [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx), sin embargo, el aprovisionamiento es sólo de entrada, lo que significa que los usuarios se importan en el directorio desde Workday y, del mismo modo, los cambios del directorio no se vuelven a escribir en Workday.

**¿Cómo puedo enviar comentarios al equipo de ingeniería?**

Póngase en contacto con nosotros a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##¿Cómo funciona el aprovisionamiento automático?

Azure AD aprovisiona a los usuarios para aplicaciones SaaS mediante la conexión de los extremos de aprovisionamiento proporcionados por el proveedor de cada aplicación. Estos extremos permiten a Azure AD crear, actualizar y quitar usuarios mediante programación. A continuación se ofrece una breve introducción de los diferentes pasos que Azure AD realiza para automatizar el aprovisionamiento.

1. Al habilitar el aprovisionamiento de una aplicación por primera vez, se realizan las siguientes acciones:
 - Azure AD intentará hacer coincidir todos los usuarios existentes en la aplicación SaaS con sus identidades correspondientes en el directorio. Cuando se asocia un usuario, *no* se habilitan automáticamente para el inicio de sesión único. Para que un usuario disponga de acceso a la aplicación, deben asignarse explícitamente a la aplicación en Azure AD, ya sea directamente o a través de la pertenencia a grupos.
 - Si ya ha especificado qué usuarios deben asignarse a la aplicación, y si Azure AD no logra encontrar cuentas existentes para tales usuarios, Azure AD aprovisionará cuentas nuevas para ellos en la aplicación.
2. Una vez que se ha completado la sincronización inicial, como se ha descrito anteriormente, Azure AD comprobará cada 10 minutos si se han producido los siguientes cambios:
 - Si se han asignado usuarios nuevos a la aplicación (directamente o a través de la pertenencia a grupos), se les aprovisionará una cuenta nueva en la aplicación SaaS.
 - Si se ha anulado el acceso de un usuario, su cuenta en la aplicación SaaS se marcará como deshabilitada (los usuarios nunca se eliminarán totalmente, lo que lo protege frente a pérdidas de datos si se producen errores de configuración).
 - Si ha asignado un usuario a la aplicación recientemente y ya tenía una cuenta en la aplicación SaaS, esa cuenta se marcará como habilitada, y algunas propiedades de usuario pueden actualizarse si están obsoletas con respecto al directorio.
 - Si se ha cambiado la información del usuario (por ejemplo, el número de teléfono, el domicilio de la oficina, etc.) en el directorio, esta información también se actualizará en la aplicación SaaS.

Para obtener más información sobre cómo se asignan los atributos entre Azure AD y la aplicación SaaS, consulte el artículo [Personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md).

##Lista de aplicaciones que admiten el aprovisionamiento automático de usuarios

Haga clic en una aplicación para ver un tutorial sobre cómo configurar el aprovisionamiento automático correspondiente:

- [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox para Empresas](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Workday](http://go.microsoft.com/fwlink/?LinkId=690250) (aprovisionamiento de entrada)

Para que una aplicación admita el aprovisionamiento automático de usuarios, primero debe proporcionar los extremos necesarios que permitan a los programas externos automatizar la creación, el mantenimiento y la eliminación de usuarios. Por lo tanto, no todas las aplicaciones SaaS son compatibles con esta característica. En el caso de las aplicaciones que sí son compatibles, el equipo de ingeniería de Azure AD podrá crear un conector de aprovisionamiento para esas aplicaciones, y las prioridades para realizar este trabajo se basan en las necesidades de los clientes actuales y potenciales.

Si desea ponerse en contacto con el equipo de ingeniería de Azure AD para solicitar soporte técnico para el aprovisionamiento de aplicaciones adicionales, envíe un mensaje a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=AcomDC_0128_2016-->
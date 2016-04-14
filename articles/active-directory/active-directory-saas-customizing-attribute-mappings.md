<properties
	pageTitle="Personalización de asignaciones de atributos | Microsoft Azure"
	description="Conozca cuáles son las asignaciones de atributos para aplicaciones SaaS en Azure Active Directory y cómo puede modificarlas para satisfacer sus necesidades empresariales."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Personalización de asignaciones de atributos


Microsoft Azure AD proporciona soporte para el aprovisionamiento de usuarios para aplicaciones SaaS de terceros como Salesforce, Google Apps y otras. Si dispone de aprovisionamiento de usuarios para una aplicación SaaS de terceros habilitada, el Portal de administración de Azure controla sus valores de atributo en forma de una configuración denominada "asignación de atributos".

Hay un conjunto preconfigurado de asignaciones de atributos entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Algunas aplicaciones administran otros tipos de objetos, como grupos o contactos. <br> Puede personalizar las asignaciones de atributos predeterminadas según sus necesidades empresariales. Esto significa que puede cambiar o eliminar asignaciones de atributos existentes o crear nuevas asignaciones de atributos.

En el portal de Azure AD, puede tener acceso a esta característica; para ello, haga clic en Atributos en la barra de herramientas de una aplicación SaaS.

> [AZURE.NOTE] El vínculo **Atributos** sólo está disponible si tiene habilitado el aprovisionamiento de usuarios para una aplicación SaaS.


![Salesforce][1]


Al hacer clic en Atributos en la barra de herramientas, aparece la lista de las asignaciones actuales configuradas para una aplicación SaaS.

La siguiente captura de pantalla le muestra un ejemplo:



![Salesforce][2]


En el ejemplo anterior, puede ver que el atributo **firstName** de un objeto administrado en Salesforce se rellena con el valor **givenName** del objeto vinculado de Azure AD.

Si desea personalizar las asignaciones de atributos o si desea revertir la configuración personalizada a la predeterminada, haga clic en el botón correspondiente en la barra de herramientas en la parte inferior de una aplicación.


![Salesforce][3]


Hay asignaciones de atributos que una aplicación SaaS necesita para funcionar correctamente. En la tabla de atributos, las asignaciones de atributos relacionadas tienen **Sí** como valor del atributo **Obligatorio**. Si se requiere una asignación de atributos, no puede eliminarla. En este caso, la característica **Eliminar** no está disponible.

Para modificar una asignación de atributos existente, seleccione la asignación y después haga clic en **Editar**. Se abrirá una página de diálogo que le permite modificar la asignación de atributos seleccionada.


![Edición de asignaciones de atributos][4]



## Información sobre los tipos de asignaciones de atributos


Con asignaciones de atributos, puede controlar cómo se rellenan los atributos en una aplicación SaaS de terceros. Se admiten cuatro tipos de asignaciones diferentes:

- **Directa**: el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.


- **Constante**: el atributo de destino se rellena con una cadena específica que se ha especificado.


- **Expresión**: el atributo de destino se rellena según el resultado de una expresión similar a un script. Para obtener más información, consulte [Escritura de expresiones para la asignación de atributos en Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Ninguno**: el atributo de destino se deja sin modificar. Sin embargo, si el atributo de destino está vacío, se rellenará con el valor predeterminado que especifique.



Además de estos cuatro tipos básicos de asignaciones de atributos, las asignaciones de atributos personalizadas admiten el concepto de una asignación de valor **predeterminada**. La asignación de valor predeterminada garantiza que un atributo de destino se rellene con un valor si no hay ningún valor en Azure AD ni en el objeto de destino.

Microsoft Azure AD proporciona una implementación muy eficaz de un proceso de sincronización. En un entorno inicializado, sólo los objetos que requieren actualizaciones se procesan durante un ciclo de sincronización. Actualizar las asignaciones de atributos repercute en el rendimiento de un ciclo de sincronización. Esto se debe a que una actualización de la configuración de la asignación de atributos requiere que se vuelvan a evaluar todos los objetos administrados. Por este motivo, es un procedimiento recomendado mantener el número mínimo de cambios consecutivos de las asignaciones de atributos.


##Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
- [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
- [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=AcomDC_0211_2016-->
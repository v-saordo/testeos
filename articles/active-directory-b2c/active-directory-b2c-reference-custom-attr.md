<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Atributos personalizados | Microsoft Azure"
	description="Uso de atributos personalizados en Azure Active Directory B2C para recopilar información sobre sus consumidores"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="swkrish"/>

#  Vista previa de Azure Active Directory B2C: Uso de atributos personalizados para recopilar información sobre los consumidores

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

El directorio de Azure Active Directory (AD) B2C incluye un conjunto integrado de atributos; por ejemplo, Nombre propio, Apellido, Ciudad, Código postal, etc. Sin embargo, todas las aplicaciones orientadas al consumidor presentan requisitos únicos en lo relativo a qué información (atributos) les gustaría recopilar de sus consumidores. Azure AD B2C permite extender su directorio (en concreto, ampliar el conjunto de atributos almacenado en cada cuenta de consumidor). También puede crear atributos personalizados en el [Portal de Azure](https://portal.azure.com/) y usarlos en las directivas de registros, como se muestra a continuación. También puede leer y escribir estos atributos mediante la API de Azure AD Graph como se muestra [aquí](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]Para personalizar los atributos, use [Extensiones de esquema de directorio de la API de Azure AD Graph](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## Creación de un atributo personalizado

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Atributos de usuario**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** para el atributo personalizado (por ejemplo, "ShoeSize") y, opcionalmente, una **Descripción**. Haga clic en **Crear**. ¡Y ya está!

    > [AZURE.NOTE]Actualmente solo está disponible el **Tipo de datos** "String". Pero iremos agregando más (DateTime, Integer, etc.) en el futuro.

El atributo personalizado ahora está disponible en la lista de **Atributos de usuario** y para usarlo en las directivas de registro.

## Uso de un atributo personalizado en la directiva de registro

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de registro**.
3. Abra la directiva de registro (por ejemplo, "B2C\_1\_SiUp") haciendo clic en ella. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **Atributos de registro** y seleccione el atributo personalizado (por ejemplo, "ShoeSize"). Haga clic en **OK**.
5. Haga clic en **Notificaciones de aplicación** y seleccione el atributo personalizado. Haga clic en **OK**. 
6. Haga clic en **Guardar** en la parte superior de la hoja. ¡Y ya está!

Puede usar la característica "Ejecutar ahora" en la directiva para comprobar la experiencia del consumidor. Ahora debe ver "ShoeSize" en la lista de atributos que se recopilan durante el registro del consumidor y en el token enviado de vuelta a la aplicación.

<!---HONumber=AcomDC_0107_2016-->
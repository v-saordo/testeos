<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Soporte técnico | Microsoft Azure"
	description="Presentación de solicitudes de soporte técnico para Azure Active Directory B2C"
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
	ms.date="02/16/2016"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Presentación de solicitudes de soporte técnico para Azure Active Directory B2C

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Puede presentar solicitudes de soporte técnico para Azure Active Directory (AD) B2C en el Portal de Azure mediante estos pasos:

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cambie del inquilino B2C a otro inquilino que tenga asociada una suscripción de Azure. Normalmente, éste es el inquilino del empleado o el inquilino predeterminado que se crea cuando se registra para una suscripción de Azure. Lea [este artículo](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad) para obtener más información acerca de la relación entre las suscripciones de Azure y los inquilinos de Azure AD.

    ![Soporte técnico: Cambiar inquilinos](./media/active-directory-b2c-support/support-switch-dir.png)

3. Después de cambiar los inquilinos, haga clic en **Ayuda y soporte técnico**.

    ![Soporte técnico: Ayuda y soporte técnico](./media/active-directory-b2c-support/support-support.png)

4. Haga clic en **Nueva solicitud de soporte técnico**.

    ![Soporte técnico: Nuevo](./media/active-directory-b2c-support/support-new.png)

5. En la hoja **Datos básicos**, use estos detalles y haga clic en **Siguiente**.

    - **Tipo de problema** es **Técnico**.
	- Elija la **Suscripción** adecuada.
    - Elija **Active Directory** en **Servicio**.
    - Elija el **Plan de soporte técnico** adecuado. Si no tiene uno, puede registrarse para obtenerlo [aquí](https://azure.microsoft.com/es-ES/support/plans/).

    ![Soporte técnico: Datos básicos](./media/active-directory-b2c-support/support-basics.png)

6. En la hoja **Problema**, use estos detalles y haga clic en **Siguiente**.

    - Elija el nivel de **Gravedad** adecuado.
    - **Tipo de problema** es **Vista previa B2C**
    - Elija la **Categoría** adecuada.
	- Describa su problema en el campo **Detalles**. Proporcione detalles como el nombre del inquilino B2C, la descripción del problema, los mensajes de error y los identificadores de correlación (si están disponibles) entre otros.
    - Proporcione la fecha y hora (incluida la zona horaria) del momento en el que se produjo el problema en el campo **Período de tiempo**.
    - Cargue todas las capturas de pantalla y los archivos que piensa que ayudarían a resolver el problema en **Carga de archivos**.

    ![Soporte técnico: Problema](./media/active-directory-b2c-support/support-problem.png)

7. En la hoja **Contacto**, agregue su información de contacto. Haga clic en **Crear**. *Nota: En la versión preliminar, el soporte técnico de Azure AD B2C solo está disponible en inglés.*

    ![Soporte técnico: Contacto](./media/active-directory-b2c-support/support-contact.png)

8. Después de enviar la solicitud de soporte técnico, puede supervisarla; para ello, haga clic en **Ayuda y soporte técnico** en el panel de inicio y, luego, en **Administrar solicitudes de soporte técnico**.

## Problema conocido: presentación de una solicitud de soporte técnico en el contexto de un inquilino B2C

Si el paso 2 descrito anteriormente no le ha salido e intenta crear una solicitud de soporte técnico en el contexto de su inquilino B2C, verá el siguiente error.

> [AZURE.IMPORTANT]
No intente registrarse para una nueva suscripción a Azure en su inquilino B2C.

![Soporte técnico - sin suscripción](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0218_2016-->
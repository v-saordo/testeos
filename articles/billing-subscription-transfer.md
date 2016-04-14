<properties
   pageTitle="Transferencia de suscripciones de Azure | Microsoft Azure"
   description="Transferencia de una suscripción de Azure a otro usuario y algunas preguntas más frecuentes (P+F) sobre el proceso"
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="12/21/2015"
   ms.author="curtand;kareni;ruchic"/>

# Transferencia de suscripciones de Azure

Entonces:

- ¿Necesita transferir la titularidad de la facturación de su suscripción de Azure a otra persona?
- ¿Desea cambiar la cuenta usada para suscribirse a Azure? ¿Quizás usó su cuenta de Microsoft pero pretendía usar su cuenta profesional o educativa?
- ¿Desea mover la suscripción de Azure de un directorio a otro?
- ¿Tiene Azure y Office 365 en distintos inquilinos y desea consolidarlos?

Puede hacerlo fácilmente en el Centro de cuentas de Microsoft Azure para las suscripciones de pago por uso, MSDN, Action Pack o BizSpark. Hemos agregado la capacidad de transferir suscripciones a otros usuarios. En otras palabras, ahora puede cambiar el administrador de la cuenta en cualquier suscripción de pago por uso, MSDN, Action Pack o BizSpark que usted posea, sin importar el país en el que trabaja.

## Transferencia de una suscripción de Azure

1.  Inicie sesión en <https://account.windowsazure.com/Subscriptions>

2.  Seleccione la suscripción que va a transferir.

3.  Haga clic en la opción **Transferir suscripción**.

    ![Pestaña Suscripciones de cuenta de Azure](./media/billing-subscription-transfer/image1.png)

4.  Siga las indicaciones para especificar al destinatario.

    ![Cuadro de diálogo Transferir suscripción](./media/billing-subscription-transfer/image2.PNG)

5.  El destinatario recibirá automáticamente un correo electrónico con un vínculo de aceptación.

    ![Correo electrónico de transferencia de suscripción a destinatario](./media/billing-subscription-transfer/image3.png)

6.  El destinatario hace clic en el vínculo y sigue las instrucciones, incluyendo la especificación de su información de pago.

    ![Página web de transferencia de primera suscripción](./media/billing-subscription-transfer/image4.PNG)

    ![Página web de transferencia de segunda suscripción](./media/billing-subscription-transfer/image5.PNG)

7. ¡Éxito! La suscripción ya está transferida.

## Preguntas más frecuentes

-   **¿Provocan las transferencias de suscripciones un tiempo de inactividad en el servicio?**

    No afectan al servicio. Esto cancela de forma efectiva la suscripción del administrador de cuenta actual y crea una nueva en la cuenta del destinatario, pero asocia los servicios subyacentes de Azure con la nueva suscripción. La Id. de la suscripción no cambia.

-   **¿Cómo puedo usar este mecanismo para cambiar el directorio de suscripción?**-   
    Las suscripciones de Azure se crean en el directorio al que pertenece el administrador de la cuenta. Por lo tanto, para cambiar el directorio, solo tiene que transferir la suscripción a una cuenta de usuario en el directorio de destino. Cuando el usuario completa los pasos para aceptar la transferencia, la suscripción se mueve automáticamente al directorio de destino.
   
-   **¿Si se hace cargo de la propiedad de la facturación de una suscripción de otra organización, seguirán teniendo acceso a mis recursos?**

    Si la suscripción se transfiere a otro inquilino, los usuarios asociados al inquilino anterior perderán el acceso a la suscripción. Aunque un usuario deje de ser administrador o coadministrador de servicios, puede seguir teniendo acceso a la suscripción a través de otros mecanismos de seguridad. Dichos mecanismos incluyen:
    - Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	Claves de acceso para servicios como Almacenamiento. Para obtener más información, consulte [Vista, copia y regeneración de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Credenciales de acceso remoto para servicios como Máquinas virtuales de Azure.

    Esta lista no está completa. El destinatario debe considerar la actualización de todos los secretos asociados al servicio si es necesario restringir el acceso a sus recursos. La mayoría de los recursos se pueden actualizar como se indican a continuación:

    1.   Vaya al portal de Azure: [*https://portal.azure.com*](https://portal.azure.com)

    2.    Haga clic en Examinar todo - &gt; Todos los recursos

    3.    Seleccione el recurso. Se abrirá la hoja de recursos.

    4.    En la hoja de recursos, haga clic en **Configuración**. Aquí puede ver y actualizar los secretos existentes.


-   **Si la suscripción se transfiere en medio del ciclo de facturación, ¿paga el destinatario todo el ciclo de facturación?**

    El remitente es el responsable del pago de cualquier uso que se notificó hasta el momento en que se completó la transferencia. El destinatario es el responsable del uso notificado desde el momento de la transferencia en adelante. Puede haber una parte del uso que se realizara antes de la transferencia, pero que se notificara con posterioridad. Dicha parte se incluirá en la factura del destinatario.

-   **¿Tiene el destinatario acceso al historial de facturación y uso?**

    En este momento, la única información que se muestra al destinatario es la cantidad de la última factura (o el saldo actual si la suscripción se transfirió antes de que se generara la primera factura). El resto del historial de facturación y uso no se transfiere con la suscripción.

-   **¿Se puede cambiar la oferta durante una transferencia?**

    La oferta debe permanecer igual. Para cambiar la oferta, es preciso [ponerse en contacto con el servicio de soporte técnico](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **¿Se puede transferir una suscripción a una cuenta de usuario de otro país?**

    No, en este momento no se admite. La cuenta de usuario del destinatario debe estar en el mismo país.

-   **¿Puede usar el destinatario un mecanismo de pago diferente?**

    Sí. Aquí existen limitaciones: ahora el historial de facturación de la suscripción se divide en dos cuentas. Pero la ventaja es que puede hacerlo sin tener que [ponerse en contacto con el servicio de soporte técnico](http://go.microsoft.com/fwlink/?LinkID=619338).

## Pasos siguientes después de aceptar la propiedad de una suscripción

1. Ahora es el administrador de cuenta. Revise y actualice la sección Administrador y coadministradores del servicio. Use la opción de Configuración del [Portal de Azure clásico](https://manage.windowsazure.com) para controlar los administradores. [Más información](http://go.microsoft.com/fwlink/?LinkID=533293).
2. También puede usar el control de acceso basado en roles (RBAC) para su suscripción y sus servicios. Visite el [Portal de Azure](https://portal.azure.com). [Más información sobre RBAC](http://go.microsoft.com/fwlink/?LinkID=544802).
3. Actualice las credenciales asociadas a los servicios de esta suscripción. Entre ellos se incluyen los siguientes:
    -   Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).
    -	Claves de acceso para servicios como Almacenamiento. Para obtener más información, consulte [Vista, copia y regeneración de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Credenciales de acceso remoto para servicios como Máquinas virtuales de Azure
4. Actualice las alertas de facturación para esta suscripción en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions). [Más información](http://go.microsoft.com/fwlink/?LinkID=533292).
5. 	Si trabaja con un asociado, considere la posibilidad de actualizar el identificador del asociado en esta suscripción. Puede hacerlo en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions).

<!---HONumber=AcomDC_1223_2015-->
<properties
   pageTitle="Creación y registro de la cuenta de publicador | Microsoft Azure"
   description="Instrucciones para crear una cuenta de desarrollador de Microsoft de manera que, tras su aprobación, se puedan vender varios tipos de productos en Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="hascipio"/>

# Crear una cuenta de desarrollador de Microsoft
Este artículo le guía a través del proceso necesario de creación y registro de cuentas para convertirse en un desarrollador de Microsoft aprobado para Azure Marketplace.

## 1\. Creación de una cuenta Microsoft
> [AZURE.WARNING] Para finalizar el proceso de publicación, deberá crear una cuenta Microsoft. Esta cuenta se utilizará para registrarse e iniciar sesión en el **Centro para desarrolladores de Microsoft** y el **Portal de publicación de Azure**. Solo debe tener una cuenta Microsoft para sus ofertas de Azure Marketplace. Esta no debe ser específica para servicios u ofertas.

La dirección que constituye el nombre de usuario debe estar en su dominio y estar controlada por su equipo de TI (por ejemplo, publishing@example.com). Los pagos, la información fiscal y los informes se tramitarán con esta cuenta.

  > [AZURE.WARNING] No se admiten palabras como "Azure" y "Microsoft" en el registro de la cuenta Microsoft. Evite el uso de estas palabras para completar el proceso de creación y registro de cuentas.

### Instrucciones

1. Cree una lista de distribución (DL) o un grupo de seguridad (SG) dentro del dominio de la compañía.
  - Agregue el equipo de incorporación a la lista de distribución.
  - La lista de distribución debe estar activa para recibir un correo electrónico de confirmación.
  - Use marketplace@example.com como dirección de correo electrónico de la lista de distribución.
  - Se debe completar en los sistemas internos.
2. Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta existente.
3. Regístrese para obtener una cuenta Microsoft usando el correo electrónico de la lista de distribución.
 - Puede registrarse para una cuenta Microsoft en [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Use marketplace@example.com como la dirección de correo electrónico.
 - Su identificador de la cuenta Microsoft es ahora marketplace@example.com.
4. Use un número de teléfono válido para el registro. El sistema le enviará un código de verificación en un mensaje de texto o realizará una llamada automática si es necesario verificar la identidad.
5. Compruebe la dirección de correo electrónico enviada a la lista de distribución.
6. Ahora está listo para usar la nueva cuenta Microsoft en el Centro para desarrolladores de Microsoft.

> [AZURE.TIP] El uso de la lista de distribución permite que varias personas reciban notificaciones de correo electrónico que son importantes para la información sobre los pagos. También garantiza que la propiedad de la cuenta Microsoft se pueda transferir y no esté vinculada a un único usuario.

## 2\. Creación de la cuenta del Centro para desarrolladores de Microsoft
El Centro para desarrolladores de Microsoft se usa para registrar la información de la empresa una sola vez. El usuario inscrito debe ser un representante válido de la compañía y debe proporcionar su información personal, como una forma de validar su identidad. La persona que se inscriba debe usar una cuenta Microsoft que se comparta en la compañía, **y esa misma cuenta debe usarse en el Portal de publicación de Azure.** Debe asegurarse de que su compañía no tenga ya una cuenta del Centro para desarrolladores de Microsoft antes de intentar crear una nueva. Durante el proceso, recopilaremos la información de dirección, los datos de la cuenta bancaria y la información fiscal de la compañía. Normalmente, estos datos se pueden obtener de contactos financieros o comerciales.

> [AZURE.IMPORTANT] Debe completar los siguientes componentes del perfil de desarrollador para avanzar por las distintas fases de la creación e implementación de ofertas.


| Perfil del desarrollador | Para iniciar el borrador | Ensayo | Publicación gratis y plantilla de solución | Publicación de ofertas comerciales |
|----|----|----|----|----|
|Registro de la compañía | Obligatorio | Obligatorio | Obligatorio | Obligatorio |
|Identificación de perfil fiscal | Opcional | Opcional | Opcional | Obligatorio |
|Cuenta bancaria | Opcional | Opcional | Opcional | Obligatorio |


> [AZURE.NOTE] El sistema "traiga su propia licencia" (BYOL) solo se admite para las máquinas virtuales y se considera una oferta **gratuita**.


### Registro de su cuenta de compañía
1. Abra una nueva sesión de exploración de InPrivate en Internet Explorer o de incógnito en Chrome para asegurarse de que no tiene una sesión iniciada en una cuenta personal.

2. Vaya a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure).

3. Inicie sesión con su cuenta Microsoft de registro de la compañía (por ejemplo, marketplace@example.com)

    ![dibujo][img-signin]

4. Complete el asistente “Ayúdenos a proteger su cuenta”, que comprobará su identidad por teléfono o por correo electrónico.

    ![dibujo][img-verify]

5. En la sección de registro e información de la cuenta, seleccione su **país o región** en el menú desplegable.

    > [AZURE.WARNING] **Países de "origen de venta":** para vender sus servicios en Azure Marketplace, la entidad registrada debe proceder de uno de los países de "origen de venta" aprobados que se mencionan arriba. Esta restricción se debe a motivos de pago e impuestos. Trabajamos activamente para ampliar esta lista de países en un futuro próximo, así pues, manténgase atento. Para más información, consulte las [directivas de participación de Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

6. Seleccione el "Tipo de cuenta", **Individual** o **Empresa**.

    > [AZURE.IMPORTANT] Para comprender mejor los tipos de cuenta y cuál es la mejor para sus necesidades, consulte la página [Tipos de cuenta, ubicaciones y precios](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx).

7. Escriba el **nombre para mostrar del publicador**, que suele ser el nombre de su compañía.

    > [AZURE.TIP] Actualmente, el Portal de publicación de Azure no utiliza el nombre para mostrar del publicador. Sin embargo, se debe rellenar para completar el proceso de registro.

8. Escriba la información de contacto de la cuenta.

    > [AZURE.IMPORTANT] La información proporcionada debe ser precisa ya que se usará en nuestro proceso de comprobación y aprobación de la compañía en el Centro para desarrolladores.

9. En el caso de una cuenta de empresa, deberá proporcionar también la información de contacto del **Aprobador de la empresa**, la persona que puede comprobar que está autorizado a crear la cuenta en nombre de su organización. Cuando haya terminado, haga clic en **Siguiente** para pasar a la **"Sección de pago"**.

10. Escriba la información de pago para pagar su cuenta. Si tiene un código de promoción que cubre el costo de registro, puede indicarlo aquí. De lo contrario, proporcione su información de tarjeta de crédito (o PayPal en mercados admitidos). Cuando haya terminado, haga clic en **Siguiente** para pasar a la **"Pantalla de revisión"**.

11. Revise la información de la cuenta y confirme que todo es correcto. A continuación, lea y acepte los términos y condiciones del [Contrato del publicador de Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Active la casilla para indicar que ha leído y aceptado estos términos.

12. Haga clic en **Finalizar** para confirmar el registro. Le enviaremos un mensaje de confirmación a su dirección de correo electrónico de desarrollador.

13. Si solo tiene pensado publicar ofertas gratuitas, haga clic en **Ir al Portal de publicación de Azure Marketplace** y vaya directamente a la sección 3 de este documento, [Registro de la cuenta en el portal de publicación](#3-register-your-account-in-the-publishing-portal).

14. Si piensa publicar ofertas comerciales, haga clic en **Actualizar la información de la cuenta** donde debe completar la información fiscal y bancaria de su cuenta del Centro para desarrolladores.

> [AZURE.IMPORTANT] No podrá probar correctamente las ofertas en el entorno de ensayo ni podrá llevar sus ofertas a producción sin completar la información fiscal y de la cuenta bancaria.

Si prefiere actualizar la información fiscal y bancaria en otro momento, puede ir a la sección 3, [Registro de la cuenta en el portal de publicación](#3-register-your-account-in-the-publishing-portal), y volver más tarde mediante los vínculos del Portal de publicación de Azure.

### Adición de información bancaria y fiscal
 Si desea publicar ofertas comerciales de compra, también necesitará agregar información de pago y fiscal y enviarla para su validación en el Centro para desarrolladores. Si solo va a publicar ofertas gratis (u ofertas BYOL), no necesita agregar esta información. Puede agregarla más adelante, pero se tarda algún tiempo en validar la información fiscal. Si sabe que ofrecerá ofertas comerciales de compra, se recomienda que las agregue tan pronto como sea posible.

**Información bancaria**

1. Inicie sesión en el [Cntro para desarrolladores de Microsoft](http://dev.windows.com/registration?accountprogram=azure) con su cuenta Microsoft.

2. Haga clic en **Cuenta de pago** en el menú de la izquierda y, en **Elegir método de pago**, haga clic en **Cuenta bancaria** o **PayPal**.

    > [AZURE.IMPORTANT] Si tiene ofertas comerciales que compran los clientes en Marketplace, esta es la cuenta donde recibirá el pago para esas compras.

3. Escriba la información de pago y, cuando esté satisfecho, haga clic en **Guardar**.

    > [AZURE.IMPORTANT] Si necesita actualizar o cambiar la cuenta de pago, siga estos mismos pasos, pero sustituya la información actual por la nueva. El hecho de cambiar la cuenta de pago puede dar lugar a retrasos en los pagos de hasta un ciclo completo. Este retraso se produce porque es necesario comprobar el cambio de la cuenta, al igual que hicimos cuando configuró por primera vez la cuenta de pago. Aunque se le seguirá pagando el importe completo después de que se haya comprobado la cuenta, los pagos pendientes del ciclo actual se agregarán al siguiente.

4. Haga clic en **Siguiente**.

**Información fiscal**

1. Inicie sesión en el [Centro para desarrolladores de Microsoft](http://dev.windows.com/registration?accountprogram=azure) con su cuenta Microsoft (si es necesario).

2. Haga clic en **Perfil fiscal** en el menú izquierdo.

3. En la página **Configure su formulario fiscal**, seleccione el país o la región donde tenga la residencia permanente y después seleccione el país o la región donde tiene la nacionalidad principal. Haga clic en **Siguiente**.

4. Escriba la información fiscal y, después, haga clic en **Siguiente**.

> [AZURE.WARNING] No podrá llevar a producción las ofertas comerciales sin completar la información fiscal y bancaria de su cuenta del Centro para desarrolladores de Microsoft.

## 3\. Registro de su cuenta en el portal de publicación
El portal de publicación de Azure se usa para publicar y administrar sus ofertas. Encontrará información útil en el portal de publicación que le guiará por todo el proceso de creación de ofertas.

> [AZURE.WARNING] Aquí deberá usar la misma cuenta Microsoft de empresa que se usó en el registro del Centro para desarrolladores de Microsoft. Se pueden agregar usuarios adicionales para ayudar una vez creada la cuenta del anunciante principal.

1.	Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta personal.

2.	Vaya a [http://publish.windowsazure.com](http://publish.windowsazure.com).

3.	Inicie sesión con su cuenta Microsoft de registro de la compañía (es decir, marketplace@example.com)) y, si es necesario, agregue coadministradores.

  > [AZURE.TIP] Las directivas de participación se describen en el [sitio web de Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si tiene problemas para completar el registro en el Centro para desarrolladores, abra una incidencia de soporte técnico tal como se indica a continuación: 1. Póngase en contacto con [Soporte técnico](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=es-ES&pesid=15635&ccsid=635847950577064286). 2. Elija **Centro de desarrolladores**. 3. Elija **Perfil**. 4. Elija un método de contacto.






## Pasos siguientes
Ahora que ya se creó y se registró la cuenta, haga clic en el tipo de artefacto (máquina virtual, servicio de programador, servicio de datos, plantilla de soluciones) que desea publicar en Azure Marketplace. Visite uno de los artículos siguientes para obtener más información sobre cómo publicar su oferta respectiva:

|| Imagen de máquina virtual | Servicio de desarrolladores | Servicio de datos | Plantilla de solución |
|----|-----|-----|-----|-----|
|**Paso 2: Creación de la oferta** |[Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)| [Requisitos previos no técnicos generales](marketplace-publishing-pre-requisites.md)|
|| [Requisitos previos técnicos de máquina virtual][link-single-vm-prereq] | Requisitos previos técnicos del servicio de desarrolladores | [Requisitos previos técnicos del servicio de datos](marketplace-publishing-data-service-creation-prerequisites.md) | [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Guía de publicación de imágenes de máquina virtual][link-single-vm] | Guía de publicación de servicios de desarrollador | [Guía de publicación de servicios de datos](marketplace-publishing-data-service-creation.md) | [Guía de publicación de plantillas de solución](marketplace-publishing-solution-template-creation.md) |
|| [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] |

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!----HONumber=AcomDC_0204_2016-->
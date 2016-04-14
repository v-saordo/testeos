<properties
    pageTitle="¿Qué es la licencia de Microsoft Azure Active Directory? | Microsoft Azure"
    description="Descripción de la licencia de Microsoft Azure Active Directory, cómo funciona, cómo comenzar y prácticas recomendadas, incluidos Office 365, Microsoft Intune y las ediciones Basic y Premium de Azure Active Directory"
    services="active-directory"
	  keywords="Licencias de Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/01/2016"
    ms.author="curtand"/>

# ¿Qué es la licencia de Microsoft Azure Active Directory?

##Descripción
Azure Active Directory (Azure AD) es la plataforma y la solución de identidad como un servicio de Microsoft. Azure AD se ofrece en distintas versiones técnicas o funcionales que van desde Azure AD Free, que se encuentra disponible con cualquier servicio de Microsoft como Office 365, Dynamics, Microsoft Intune y Azure (Azure AD no genera ningún gasto de consumo en este modo), hasta las versiones de pago de Azure AD como Enterprise Mobility Suite (EMS), Azure AD Premium y Basic y Azure Multi-Factor Authentication (MFA). Al igual que muchos de los servicios en línea de Microsoft, la mayoría de las versiones de pago de Azure AD se proporcionan a través de derechos por usuario, como ocurre en Office 365, Microsoft Intune y Azure AD. En estos casos, la compra de servicio se representa con una o varias suscripciones, y cada suscripción incluye un número de preventa de licencias en el inquilino. Los derechos de usuario se logran a través de la asignación de licencias, creando un vínculo entre el usuario y el producto, habilitando los componentes del servicio para el usuario y consumiendo una de las licencias de prepagadas.

[Probar Azure AD Premium ahora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portal de administración de Azure AD es una parte del Portal de Azure clásico. Aunque para usar de Azure AD no es preciso adquirir Azure, el acceso a este portal requiere una suscripción activa a Azure o una [suscripción de prueba a Azure](https://azure.microsoft.com/pricing/free-trial/).

Para obtener una extensa información general de las capacidades de servicio de Azure AD, consulte [¿Qué es Azure AD?](active-directory-whatis.md) [Obtenga más información acerca de los niveles de servicio de Azure AD.](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Las suscripciones de Azure de pago por uso de Azure son distintas: a pesar de que también se representan en el directorio, estas suscripciones permiten la creación de recursos de Azure y asignarlos a la forma de pago. En este caso, NO hay ningún número de licencias asociado a la suscripción. La asociación de los usuarios con la suscripción, el acceso de los usuarios a los recursos de suscripción de administración, se consigue mediante la concesión de permisos para que funcione en recursos de Azure asignados a la suscripción.


##¿Cómo funcionan las licencias de Azure AD?

Los servicios de Azure AD basados en licencias (basados en derechos) funcionan mediante la activación de una suscripción en el inquilino del servicio/directorio de Azure AD. Una vez que la suscripción está activa, los administradores de servicios/directorios pueden gestionar las capacidades del servicio y los usuarios con licencia pueden usarlas.

Cuando compra o activa Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, el directorio se actualiza con la suscripción, incluido el período de validez y las licencias de prepago. La información de suscripción, incluido el estado, el próximo evento de ciclo de vida y el número de licencias asignadas o disponibles se encuentra a su disposición a través del Portal de Azure clásico, en la pestaña Licencias para el directorio específico. Es también la mejor solución para llevar a cabo la administración de las asignaciones de licencia.

Cada suscripción consta de uno o varios planes de servicio, que representan el nivel funcional incluye del tipo de servicio; por ejemplo, Azure AD, Azure MFA, Microsoft Intune, Exchange Online o SharePoint Online. Administración de licencias de Azure AD no requiere administración nivel del plan de servicio. Esto es diferente de Office 365, que se basa en este modo de configuración avanzada para administrar el acceso a los servicios incluidos. Azure AD se basa en la configuración del servicio para habilitar las características y administrar los permisos individuales.

En general, la información de suscripción de Azure AD se administra a través del Portal de Azure clásico, en la pestaña Licencias para el directorio específico. Las suscripciones de Azure AD, con la excepción de Azure AD Premium, no se muestran en el portal de Office.

> [AZURE.IMPORTANT] Azure AD Premium y Basic, así como las suscripciones de Enterprise Mobility Suite, se limitan a su inquilino/directorio aprovisionado. Las suscripciones no se pueden dividir entre directorios ni usarse para autorizar a los usuarios en otros directorios. Es posible mover una suscripción entre directorios, pero requiere el envío de una incidencia de soporte técnico o la cancelación y compra de nuevo en caso de compras directas.

> Al adquirir Azure AD o Enterprise Mobility Suite a través del programa de licencias por volumen la activación de la suscripción se realizará automáticamente cuando el contrato incluya otros servicios de Microsoft Online Services, como por ejemplo, Office 365.

Las características de Azure AD de pago dividen el alcance del directorio. Los ejemplos incluyen: - Asignación basada en grupos a aplicaciones, que se habilita según la aplicación específica que está administrando. - Las capacidades de administración de grupo avanzadas y de autoservicio están disponibles según la configuración del directorio o dentro del grupo específico. - Los informes de seguridad premium se encuentran en la pestaña Informes. - La detección de aplicaciones en la nube aparece en el portal de Azure en Identidad.

###Asignación de licencias
Cuando obtiene una suscripción, todo lo que tiene que hacer es configurar las capacidades de pago. El uso de características de pago de Azure AD requiere la distribución de licencias a las personas adecuadas. Por regla general, se debe asignar una licencia a los usuarios que deban disponer de acceso o que puedan administrarse a través de una característica de pago de Azure AD. Una asignación de licencia es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise Mobility Suite.

Administrar qué usuarios del directorio deben tener una licencia es sencillo. Puede realizarse mediante la asignación a un grupo para crear reglas de asignación a través del portal de administración de Azure AD, o asignando licencias directamente a las personas adecuadas a través de un portal, PowerShell o API. Al asignar licencias a un grupo, se asignará una licencia a todos los miembros del grupo. Si se agregan usuarios o se quitan de un grupo, se asignarán a la licencia adecuada o se quitarán de ella. La asignación de grupo puede utilizar cualquier administración de grupo disponible para usted y que sea coherente con la asignación basada en grupo para las aplicaciones. Con ese enfoque, puede configurar reglas como que todos los usuarios del directorio se asignen automáticamente, garantizar que todas las personas con el puesto adecuado dispongan de licencia o incluso delegar la decisión a otros administradores de la organización.

Con la asignación de licencias basada en grupo, los usuarios que no dispongan de una ubicación de uso heredarán la ubicación del directorio durante la asignación. El administrador puede cambiar esta ubicación en cualquier momento. En casos en los que se produzca un error en la asignación automatizada, la información de usuario de ese tipo de licencia reflejará ese estado.

##Introducción a licencias de Azure AD

La introducción a Azure AD es sencilla; siempre puede crear un directorio como parte del registro en una prueba gratuita de Azure. [Obtenga más información acerca del registro como organización](sign-up-organization.md). La siguiente información puede ayudarle a asegurarse de que su directorio se alinea mejor con otros servicios de Microsoft que esté consumiendo o que piense consumir, y sus objetivos en cuanto a la obtención del servicio.

Estas son un par de prácticas recomendadas: - Si ya está usando servicios organizativos de Microsoft, ya dispone de un directorio de Azure AD. En este caso, debe continuar usando el mismo directorio para otros servicios, por lo que la administración de identidades principal, incluido el aprovisionamiento y el SSO híbrido, pueden utilizarse en los servicios. Los usuarios dispondrán una experiencia de inicio de sesión única y se beneficiarán de capacidades más completas en los servicios. Como resultado, si decide comprar un servicio de pago de Azure AD para sus recursos, le recomendamos que use el mismo directorio cuando lo haga. - Si piensa usar Azure AD para un conjunto distinto de usuarios (partners, clientes, etc.), o si quiere evaluar los servicios de Azure AD y quiere hacerlo aisladamente del servicio de producción, o si busca configurar un entorno de espacio aislado para los servicios, recomendamos que primero cree un nuevo directorio a través del portal de Azure clásico. [Puede obtener más información acerca de la creación de un nuevo directorio de Azure AD en el portal de Azure clásico](active-directory-licensing-directory-independence.md). El nuevo directorio se creará con su cuenta como un usuario externo con permisos de administrador globales. Cuando inicie sesión en el portal de Azure clásico con esta cuenta, podrá ver este directorio y obtener acceso a todas las tareas de administración de directorios. Recomendamos que cree una cuenta local con privilegios apropiados para administrar otros servicios de Microsoft (aquellos a los que no puede obtener acceso a través del portal de Azure clásico). [Obtenga más información sobre la creación de cuentas de usuario en Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD admite "usuarios externos", que son cuentas de usuario en una instancia de Azure AD que se generaron con una identidad de Azure AD o cuenta Microsoft (MSA) desde otro directorio. Puesto que estamos ocupados ampliando esta capacidad a todos los servicios organizacionales de Microsoft, estas cuentas no son actualmente compatibles en algunas de las experiencias de servicios; por ejemplo, el Portal de administración de Office 365 no es compatible actualmente con estos usuarios. Como resultado, los usuarios externos con cuentas Microsoft no podrán obtener acceso al Portal de administración de Office 365 mientras que los usuarios externos de otros directorios de Azure AD se ignorarán. En el último caso, solo se podrá obtener acceso a la cuenta local del usuario, el directorio de Azure AD u Office 365 en el que se creó originalmente el usuario, a través de estas experiencias.

Como se indica, Azure AD dispone de distintas versiones de pago. Estas versiones apenas se diferencian en cuanto a disponibilidad de compra:


| Producto | EA/VL | Abrir | 	CSP | 	Derechos de uso de MPN | 	Compra directa | Versión de prueba |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Basic | X | X | X | X | <br /> | <br /> |

###Seleccione una o más versiones de prueba de licencia
 En todos los casos, puede activar una suscripción de prueba de Azure AD Premium o Enterprise Mobility Suite seleccionando la versión de prueba específica que desee en la pestaña Licencias en su directorio. Cualquier versión de prueba contiene una suscripción de 30 días con 100 licencias.

![Planes de licencia de prueba de Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Planes de licencia de prueba de Enterprise Mobility Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Planes de licencia de prueba activa](./media/active-directory-licensing-what-is/active_license_trials.png)

###Asignación de licencias
Una vez que la suscripción esté activa, debe asignarse una licencia a usted mismo y actualizar el explorador para asegurarse de que ve todas las características. El siguiente paso es asignar licencias a los usuarios que necesitarán tener acceso a características de pago de pago de Azure AD o que tendrán que incluirse en ellas. Como se mencionó anteriormente en "Asignación de licencias", la mejor forma de hacer esto es identificar el grupo que representa a la audiencia deseada y asignarle la licencia; de esta forma, los usuarios que se agreguen o se quiten  
del grupo durante su ciclo de vida se asignarán a la licencia o se quitarán de ella.

Para asignar una licencia a un grupo o a usuarios individuales, seleccione el plan de licencia que desee asignar y haga clic en **Asignar** en la barra de comandos.

![Planes de licencia de prueba activa](./media/active-directory-licensing-what-is/assign_licenses.png)

En el cuadro de diálogo de asignación del plan deseado puede seleccionar los usuarios y agregarlos a la columna **Asignar** de la derecha. Puede desplazarse por la lista de usuarios o buscar personas específicas con la lupa que aparece en la parte superior derecha de la cuadrícula del usuario. Para asignar grupos, seleccione "Grupos" en el menú **Mostrar** y luego haga clic en el botón de comprobación de la derecha para actualizar las asignaciones que se muestran.

![Asignación de licencias a grupos](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Ahora puede buscar o desplazarse por los grupos y agregarlos a la columna **Asignar** de la misma manera. Puede utilizarlos para asignar una combinación de usuarios y grupos en una operación única. Para completar el proceso de asignación, haga clic en el botón de comprobación en la esquina inferior derecha de la página.

![Mensaje de progreso de asignación de licencias](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Cuando se asigna un grupo, sus miembros heredan las licencias en un plazo de 30 minutos, aunque normalmente lo hacen en 1-2 minutos.

Se pueden producir errores de asignación durante la asignación de licencias de Azure AD, pero son relativamente poco frecuentes. Los posibles errores de asignación se limitan a los siguientes: - Conflicto de asignación: cuando se asignó previamente una licencia que no es compatible con la licencia actual. En este caso, la asignación de la nueva licencia requerirá quitar la anterior. - Número excesivo de licencias disponibles: cuando el número de usuarios en grupos asignados supera las licencias disponibles, el estado de la asignación de los usuarios reflejará un error de asignación debido a que faltan licencias.

###Visualización de licencias asignadas

En la pestaña **Licencias** aparece una vista de resumen de las licencias asignadas, que incluye las disponibles, las asignadas y el siguiente evento de ciclo de vida de la suscripción.

![Visualización del número de licencias asignadas](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Hay disponible una lista detallada de usuarios y grupos asignados, incluida la ruta de acceso y el estado de la asignación (directa o heredada de uno o más grupos) cuando se dirige a un plan de licencia.

![Visualización de detalles de licencias asignadas para un plan de licencias](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Quitar licencias es tan fácil como asignarlas. Si el usuario se asigna directamente o en el caso de que un grupo asignado, puede quitar la licencia, para lo que debe seleccionar el tipo de licencia y luego seleccionar **Quitar**, agregar el usuario o el grupo a la lista de eliminación y, finalmente, confirmar la acción. Como alternativa, puede abrir un tipo de licencia, seleccionar el usuario o grupo específicos y pulsar **Quitar** en la barra de comandos. Para acabar con la herencia de una licencia de un usuario de un grupo, simplemente quite el usuario del grupo.

###Ampliación de pruebas

Las extensiones de prueba para los clientes están disponibles como autoservicio a través del Portal de Office 365. Un administrador de clientes puede navegar al [Portal de Office](https://portal.office.com/#Billing) (el acceso depende de los permisos para el Portal de Office) y seleccionar la prueba de Azure AD Premium. Haga clic en el vínculo **Ampliar prueba** y siga las instrucciones. Deberá especificar una tarjeta de crédito, pero no se le cobrará.

![Ampliación de una versión de prueba de licencia en el Portal de Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Los clientes también pueden solicitar una extensión de prueba enviando una solicitud de soporte técnico. Un administrador de clientes puede navegar a la [página de soporte técnico](http://aka.ms/extendAADtrial) del Portal de Office 365 (el acceso depende de los permisos para la página de soporte técnico de Office). En esta página, seleccione "Suscripciones y versiones de prueba" en Características y "Preguntas de prueba" en Síntoma. Por último, escriba la información sobre las circunstancias.

![Ampliación de una versión de prueba de licencia mediante una solicitud de soporte técnico](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## Pasos siguientes

Ahora ya está preparado para configurar y usar algunas características de Azure AD Premium

- [Restablecimiento de la contraseña de autoservicio](active-directory-manage-passwords.md)
- [Administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
- [Estado de Azure AD Connect](active-directory-aadconnect-health.md)
- [Asignación de grupo a aplicaciones](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Compra directa de licencias de Azure AD Premium](http://aka.ms/buyaadp)

<!---HONumber=AcomDC_0204_2016-->
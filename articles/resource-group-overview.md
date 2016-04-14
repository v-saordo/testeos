<properties
   pageTitle="Información general del Administrador de recursos de Azure | Microsoft Azure"
   description="Describe cómo utilizar Administrador de recursos de Azure para la implementación, la administración y el control de acceso de los recursos en Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="tomfitz"/>

# Información general del Administrador de recursos de Azure

La infraestructura de la aplicación está constituida normalmente por varios componentes: quizás una máquina virtual, una cuenta de almacenamiento y una red virtual, o una aplicación web, una base de datos, un servidor de bases de datos y servicios de terceros. Estos componentes no se ven como entidades independientes, sino como partes de una sola entidad relacionadas e interdependientes. Desea implementarlas, administrarlas y supervisarlas como grupo. Administrador de recursos de Azure permite trabajar con los recursos de la solución como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la solución en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Administrador de recursos proporciona funciones de seguridad, auditoría y etiquetado que le ayudan a administrar los recursos después de la implementación.

## Ventajas de usar Administrador de recursos

Administrador de recursos ofrece varias ventajas:

- Puede implementar, administrar y supervisar todos los recursos para su solución como grupo, en lugar de controlar estos recursos individualmente.
- Puede implementar la solución repetidamente a lo largo del ciclo de vida del desarrollo y tener la seguridad de que los recursos se implementan de forma coherente.
- Puede utilizar plantillas declarativas para definir la implementación.
- Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.
- Puede aplicar control de acceso a todos los servicios del grupo de recursos al integrarse de forma nativa Control de acceso basado en rol (RBAC) en la plataforma de administración.
- Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos en su suscripción.
- Puede aclarar la facturación de la organización consultando los costos acumulados de todo el grupo o para un grupo de recursos que comparten la misma etiqueta.  

El Administrador de recursos ofrece una nueva manera de implementar y administrar las soluciones. Si usó el anterior modelo de implementación y desea obtener más información sobre los cambios, consulte [Descripción de la implementación de Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md).

## Guía

Las siguientes sugerencias le ayudarán a sacar el máximo partido de Administrador de recursos al trabajar con soluciones.

1. Defina e implemente la infraestructura mediante la sintaxis declarativa en las plantillas de Administrador de recursos, en lugar de comandos imperativos.
2. Defina todos los pasos de implementación y configuración de la plantilla. No debería tener ningún paso manual para configurar la solución.
3. Ejecute comandos imperativos para administrar los recursos, como iniciar o detener una aplicación o un equipo.
4. Organice los recursos con el mismo ciclo de vida en un grupo de recursos. Use etiquetas para organizar los demás recursos.

## Grupos de recursos

Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. El grupo de recursos puede incluir todos los recursos de una aplicación o solo aquellos que se agrupan juntos lógicamente. Puede decidir cómo desea asignar los recursos a los grupos de recursos en función de lo que más convenga a su organización.

Hay algunos factores importantes que se deben tener en cuenta al definir el grupo de recursos:

1. Todos los recursos del grupo deberían compartir el mismo ciclo de vida. Se implementarán, actualizarán y eliminarán de forma conjunta. Si un recurso, como un servidor de base de datos, debe existir en un ciclo de implementación diferente, debe estar en otro grupo de recursos.
2. Cada recurso solo puede existir en un grupo de recursos.
3. Puede agregar o quitar un recurso de un grupo de recursos en cualquier momento.
4. Puede mover un recurso de un grupo de recursos a otro. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).
4. Un grupo de recursos puede contener recursos que residen en diferentes regiones.
5. Un grupo de recursos puede utilizarse para definir el ámbito de control de acceso para las acciones administrativas.
6. Se puede vincular un recurso a otro recurso de un grupo de recursos distinto cuando los dos recursos deben interactuar entre sí pero no comparten el mismo ciclo de vida (por ejemplo, varias aplicaciones que se conectan a una base de datos). Para obtener más información, consulte [Vinculación de recursos en el Administrador de recursos de Azure](resource-group-link-resources.md).

## Proveedores de recursos

Un proveedor de recursos es un servicio que proporciona los recursos que puede implementar y administrar mediante el Administrador de recursos. Cada proveedor de recursos ofrece operaciones de API de REST para trabajar con los recursos. Por ejemplo, si desea implementar un Almacén de claves de Azure para almacenar claves y secretos, trabajará con el proveedor de recursos **Microsoft.KeyVault**. Este proveedor de recursos ofrece un tipo de recursos denominados **almacenes** para crear el almacén de claves, un tipo de recurso denominado **almacenes o secretos** para crear un secreto en el almacén de claves y un conjunto de [operaciones de API de REST](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Para implementar y administrar la infraestructura, debe conocer los detalles acerca de los proveedores de recursos como, por ejemplo, los tipos de recurso que ofrece, los números de versión de las operaciones de API de REST, las operaciones que admite y el esquema que se usará al establecer los valores del tipo de recurso que desea crear. Para información sobre los proveedores de recursos admitidos, consulte [proveedores del Administrador de recursos, regiones, versiones de API y esquemas](resource-manager-supported-services.md).

## Implementación de plantilla

Con Administrador de recursos, puede crear una plantilla sencilla (en formato JSON) que define la implementación y configuración de la aplicación. Esta plantilla se conoce como plantilla de Administrador de recursos y proporciona una manera declarativa de definir la implementación. El uso de una plantilla permite implementar la aplicación repetidamente a lo largo del ciclo de vida de esta y tener la seguridad de que los recursos se implementan de forma coherente.

Dentro de la plantilla puede definir la infraestructura de la aplicación, cómo configurar dicha infraestructura y cómo publicar el código de aplicación en ella. No necesita preocuparse por el orden de implementación porque Administrador de recursos de Azure analiza las dependencias para asegurarse de que los recursos se crean en el orden correcto. Para obtener más información, consulte [Definición de dependencias en plantillas del Administrador de recursos de Azure](resource-group-define-dependencies.md).

No es necesario definir toda la infraestructura en una sola plantilla. A menudo, tiene sentido dividir los requisitos de implementación en un conjunto de plantillas seleccionadas, específicas para un propósito. Puede fácilmente volver a usar estas plantillas para distintas soluciones. Para implementar una solución determinada, cree una plantilla maestra que vincule todas las plantillas necesarias. Para más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

También puede utilizar la plantilla para las actualizaciones de la infraestructura. Por ejemplo, puede agregar un nuevo recurso a la aplicación y agregar reglas de configuración para los recursos que ya están implementados. Si la plantilla especifica la creación de un nuevo recurso pero ese recurso ya existe, Administrador de recursos de Azure realiza una actualización en lugar de crear un nuevo recurso. Administrador de recursos de Azure actualiza el activo existente al mismo estado que tendría si fuese nuevo. O bien, puede especificar que el Administrador de recursos elimine todos los recursos no especificados en la plantilla. Para conocer las diferentes opciones al implementar, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

Puede especificar parámetros en la plantilla para permitir la personalización y conseguir flexibilidad en la implementación. Por ejemplo, puede pasar valores de parámetro que adapten la implementación al entorno de prueba. Mediante la especificación de parámetros, puede utilizar la misma plantilla para la implementación en todos los entornos de la aplicación.

Administrador de recursos proporciona extensiones para escenarios en los que se necesitan operaciones adicionales, como la instalación de un software determinado que no está incluido en el programa de instalación. Si ya utiliza un servicio de administración de configuración, como DSC, Chef o Puppet, puede seguir trabajando con ese servicio mediante las extensiones.

Cuando crea una solución desde Marketplace, la solución incluye automáticamente una plantilla de implementación. No tiene que crear la plantilla desde cero, puede empezar con la plantilla para la solución y personalizarla para satisfacer sus necesidades específicas.

Por último, la plantilla se convierte en parte del código fuente de la aplicación. Puede protegerla en el repositorio de código fuente y actualizarla a medida que evoluciona la aplicación. Puede editar la plantilla mediante Visual Studio.

Para obtener más información sobre la creación de plantillas, consulte [Crear plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).

Para más información acerca del uso de una plantilla para la implementación, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

Para obtener instrucciones sobre cómo estructurar las plantillas, consulte [Procedimientos recomendadas para diseñar plantillas del Administrador de recursos de Azure](best-practices-resource-manager-design-templates.md).

Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).

## Etiquetas

Administrador de recursos proporciona una característica de etiquetado que permite clasificar los recursos de acuerdo con los requisitos de administración o facturación. Es recomendable usar etiquetas cuando se tiene un conjunto complejo de grupos de recursos y de recursos, y se necesitan visualizar estos activos de la manera más conveniente. Por ejemplo, puede etiquetar recursos que cumplen una función similar en la organización o que pertenecen al mismo departamento. Sin etiquetas, los usuarios de su organización pueden crear varios recursos que son muy difíciles de identificar y administrar. Por ejemplo, puede que desee eliminar todos los recursos de un proyecto concreto, pero si no se han etiquetado esos recursos en el proyecto, tendrá que buscarlos manualmente. El etiquetado puede ser un aspecto importante para reducir costos innecesarios en su suscripción.

Los recursos no tienen que residir en el mismo grupo de recursos para compartir una etiqueta. Puede crear su propia taxonomía de etiquetas para asegurarse de que todos los usuarios de la organización utilizan etiquetas comunes y no aplican accidentalmente etiquetas ligeramente diferentes (por ejemplo, "dept" en lugar de "departamento").

Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md). Puede crear una [directiva personalizada](#manage-resources-with-customized-policies) que requiera agregar etiquetas a los recursos durante la implementación.

## Control de acceso

Administrador de recursos permite controlar quién tiene acceso a acciones específicas para la organización. Integra de forma nativa OAuth y Control de acceso basado en rol (RBAC) en la plataforma de administración y aplica este control de acceso a todos los servicios del grupo de recursos. Puede agregar usuarios a roles específicos de plataforma y recurso predefinidos, y aplicar estos roles a una suscripción, un grupo de recursos o un recurso para limitar el acceso. Por ejemplo, puede aprovechar el rol predefinido denominado Colaborador de Base de datos de SQL que permite a los usuarios administrar bases de datos pero no servidores de base de datos ni directivas de seguridad. Puede agregar usuarios de la organización que necesitan este tipo de acceso al rol Colaborador de Base de datos de SQL y aplicar el rol a la suscripción, al grupo de recursos o al recurso.

Administrador de recursos registra automáticamente las acciones del usuario para la auditoría. Para más información sobre cómo trabajar con los registros de auditoría, consulte [Operaciones de auditoría con el Administrador de recursos](resource-group-audit.md).

Para más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). El tema [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md) contiene una lista de roles integrados y acciones permitidas. Los roles integrados incluyen roles generales como propietario, lector y colaborador, así como roles específicos del servicio como colaborador de la máquina virtual, colaborador de la red virtual y administrador de seguridad SQL (por nombrar solo algunos de los roles disponibles).

También puede bloquear explícitamente recursos críticos para impedir que los usuarios los eliminen o modifiquen. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

Para conocer las prácticas recomendadas, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).

## Administración de recursos con directivas personalizadas

El Administrador de recursos permite crear directivas personalizadas para administrar los recursos. Los tipos de directivas que cree pueden incluir escenarios tan diversos como aplicar una convención de nomenclatura de recursos, limitar qué tipos e instancias de recursos se pueden implementar, limitar qué regiones pueden hospedar un tipo de recurso o necesitar un valor de etiqueta en recursos para organizar la facturación por departamentos. Puede crear directivas para ayudar a reducir costos y mantener la coherencia en la suscripción. Para obtener más información, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

## Capa de administración coherente

El Administrador de recursos proporciona operaciones totalmente compatibles a través de Azure PowerShell, CLI de Azure para Mac, Linux y Windows, Portal de Azure o la API de REST. Puede utilizar la interfaz que más le convenga y moverse rápidamente entre las interfaces sin confusión. El portal incluso muestra notificación sobre las acciones realizadas fuera de él.

Para obtener información sobre PowerShell, consulte [Uso de Azure PowerShell con Administrador de recursos](./powershell-azure-resource-manager.md) y [Cmdlets de Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obtener información sobre CLI de Azure, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con administración de recursos de Azure](./xplat-cli-azure-resource-manager.md)).

Para obtener información acerca de la API de REST, consulte [Referencia de la API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Para más información sobre cómo usar el portal, consulte [Uso del Portal de Azure para administrar los recursos de Azure](azure-portal/resource-group-portal.md).

El Administrador de recursos de Azure admite el uso compartido de recursos entre orígenes (CORS). Con CORS, puede llamar a la API de REST del Administrador de recursos o un API de REST de un servicio Azure desde una aplicación web que reside en un dominio diferente. Sin compatibilidad con CORS, el explorador web evitaría que una aplicación en un dominio acceda a recursos de otro dominio. El Administrador de recursos habilita CORS para todas las solicitudes con credenciales de autenticación válidas.

## Pasos siguientes

- Para obtener información sobre cómo crear plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para implementar la plantilla que creó, consulte [Implementación de plantillas](resource-group-template-deploy.md)
- Para comprender las funciones que puede usar en una plantilla, consulte [Funciones de plantillas](./resource-group-template-functions.md)
- Para obtener instrucciones sobre cómo diseñar las plantillas, consulte [Prácticas recomendadas para diseñar plantillas del Administrador de recursos de Azure](best-practices-resource-manager-design-templates.md).

La siguiente es una demostración de esta introducción.

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0204_2016-->
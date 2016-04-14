<properties
   pageTitle="Configuración de su proyecto Azure mediante varias configuraciones de servicio | Microsoft Azure"
   description="Cambie los archivos ServiceDefinition.csdef y ServiceConfiguration.cscfg para saber cómo configurar un proyecto de servicio en la nube de Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Configuración de su proyecto Azure mediante varias configuraciones de servicio

Un proyecto de servicio en la nube de Azure incluye dos archivos de configuración: ServiceDefinition.csdef y ServiceConfiguration.cscfg. Estos archivos están empaquetados con la aplicación de servicio en la nube de Azure y se implementan en Azure.

- El archivo **ServiceDefinition.csdef** contiene los metadatos necesarios en el entorno de Azure para los requisitos de la aplicación de servicio en la nube, incluso los roles que contiene. Este archivo también contiene valores de configuración que se aplican a todas las instancias. Estos valores de configuración se pueden leer en tiempo de ejecución mediante la API de tiempo de ejecución de hospedaje de servicios de Azure. Este archivo no se puede actualizar mientras el servicio se ejecuta en Azure.

- El archivo **ServiceConfiguration.cscfg** establece los valores de configuración definidos en el archivo de definición de servicio y especifica el número de instancias que se ejecutarán para cada rol. Este archivo se puede actualizar mientras el servicio en la nube se ejecuta en Azure.

Azure Tools para Microsoft Visual Studio proporciona páginas de propiedades que se pueden usar para establecer los valores de configuración almacenados en estos archivos. Para tener acceso a las páginas de propiedades, haga doble clic en la referencia del rol bajo el proyecto de servicio en la nube de Azure en el Explorador de soluciones o haga clic con el botón derecho en la referencia del rol y elija **Propiedades**, como se muestra en la siguiente ilustración.

![VS\_Solution\_Explorer\_Roles\_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obtener información acerca de los esquemas subyacentes para los archivos de configuración y definición de servicio, consulte [Referencia de esquemas](https://msdn.microsoft.com/library/azure/dd179398.aspx). Para obtener más información sobre la configuración del servicio, consulte [Configuración de servicios en la nube](/cloud-services/cloud-services-how-to-configure.md).

## Configuración de las propiedades de rol

Las páginas de propiedades de un rol web y un rol de trabajo son similares, salvo por las diferencias que se indican en las secciones siguientes.

En la página **Almacenamiento en caché**, puede configurar los servicios de almacenamiento en caché de Azure.

### Página Configuración

En la página **Configuración**, puede establecer estas propiedades:

**Instancias.**

Establezca la propiedad **Número de instancias** en el número de instancias que el servicio debe ejecutar para este rol

Establezca la propiedad **Tamaño de VM** en **Extra pequeño**, **Pequeño**, **Mediano**, **Grande** o **Extra grande**. Para obtener más información, consulte [Tamaños de los servicios en la nube](/cloud-services/cloud-services-sizes-specs.md).

**Acción de inicio** (solo para el rol web)

Establezca esta propiedad para especificar que Visual Studio debería iniciar un explorador web para los extremos HTTP, los extremos HTTPS o ambos al iniciar la depuración.

La opción de extremo HTTPS solo está disponible si ya se ha definido un extremo HTTPS para el rol. Puede definir un extremo HTTPS en la página de propiedades **Extremos**.

Si ya se ha agregado un extremo HTTPS, la opción de extremo HTTPS se habilita de forma predeterminada, y Visual Studio iniciará un explorador para este extremo al comenzar la depuración, además de a un explorador para el extremo HTTP. Da por supuesto que están habilitadas las dos opciones de inicio.

**Diagnóstico**

De manera predeterminada, la funcionalidad de diagnóstico está habilitada para el rol web. El proyecto de servicio en la nube de Azure y la cuenta de almacenamiento se establecen para usar el emulador de almacenamiento local. Cuando esté listo para realizar la implementación en Azure, puede hacer clic en el botón del generador (**…**) para actualizar la cuenta de almacenamiento de modo que use el almacenamiento de Azure en la nube. Los datos de diagnóstico se pueden transferir a la cuenta de almacenamiento a petición o a intervalos programados automáticamente. Para obtener más información sobre los diagnósticos de Azure, consulte [Habilitación de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](/cloud-services/cloud-services-dotnet-diagnostics.md).

## Página Configuración

En la página **Configuración**, se pueden agregar valores de configuración para el servicio. Los valores de configuración son pares nombre-valor. El código que se está ejecutando en el rol puede leer los valores de sus valores de configuración en tiempo de ejecución usando las clases proporcionadas por la [biblioteca administrada de Azure](http://go.microsoft.com/fwlink?LinkID=171026). En concreto, el método [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) devuelve un valor de configuración con nombre en tiempo de ejecución.

### Configuración de una cadena de conexión para una cuenta de almacenamiento

Una cadena de conexión es un valor de configuración que proporciona información de conexión y autenticación para el emulador de almacenamiento o para una cuenta de almacenamiento de Azure. Siempre que el código deba tener acceso a datos de servicios de almacenamiento de Azure, es decir, datos de blob, cola o tabla, desde el código que se ejecuta en un rol, necesitará definir una cadena de conexión para esa cuenta de almacenamiento.

Una cadena de conexión que señala a una cuenta de almacenamiento de Azure debe usar un formato específico. Para obtener información sobre cómo crear cadenas de conexión, consulte [Configuración de cadenas de conexión de Almacenamiento de Azure](/storage/storage-configure-connection-string.md).

Cuando esté preparado para probar su servicio con los servicios de almacenamiento de Azure o cuando lo esté para implementar su servicio en la nube en Azure, puede cambiar el valor de cualquier cadena de conexión de modo que señale a su cuenta de almacenamiento de Azure. Haga clic en (**...**), seleccione **Especificar credenciales de cuenta de almacenamiento**. Escriba la información de la cuenta, incluidos el nombre y la clave de acceso. En el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**, puede indicar también si desea usar los extremos HTTPS predeterminados (opción predeterminada), los extremos HTTP predeterminados o extremos personalizados. Puede decidir usar extremos personalizados si ha registrado un nombre de dominio personalizado para su servicio, tal y como se describe en [Configuración de un nombre de dominio personalizado para datos Blob en una cuenta de almacenamiento de Azure](/storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Debe modificar las cadenas de conexión de modo que señalen a una cuenta de almacenamiento de Azure antes de implementar su servicio. De no hacerlo, es posible que su rol no se inicie, o pase de inicializarse a no estar disponible, para detenerse finalmente.

## Página Extremos

Un rol de trabajador puede tener un número indeterminado de extremos HTTP, HTTPS o TCP. Los extremos pueden ser extremos de entrada (que están disponibles para los clientes externos) o extremos internos (que están disponibles para otros roles que se ejecuten dentro del servicio).

- Para hacer que un extremo HTTP esté disponible para clientes externos y exploradores web, cambie el tipo del extremo para que sea de entrada, y especifique un nombre y un número de puerto público.

- Para hacer que un extremo HTTPS esté disponible para clientes externos y exploradores web, cambie el tipo del extremo para que sea de **entrada**, y especifique un nombre, un número de puerto público y un nombre de certificado de administración.

    Tenga en cuenta que, para poder especificar un certificado de administración, debe definir el certificado en la página de propiedades **Certificados**.

- Para hacer que un extremo esté disponible para el acceso interno de otros roles del servicio en la nube, cambie el tipo del extremo para que sea interno, y especifique un nombre y los puertos privados posibles para el extremo.

## Pagina Almacenamiento local

Puede usar la página de propiedades **Almacenamiento local** para reservar uno o más recursos de almacenamiento local para un rol. Un recurso de almacenamiento local es un directorio reservado en el sistema de archivos de la máquina virtual de Azure donde se ejecuta una instancia de un rol. Para obtener más información acerca de cómo trabajar con los recursos de almacenamiento local, vea [Configurar los recursos de almacenamiento local](/cloud-services/cloud-services-configure-local-storage-resources.md).

## Página Certificados

En la página **Certificados**, puede asociar certificados a un rol. Los certificados que agregue se pueden usar para configurar los extremos HTTPS en la página de propiedades **Extremos**.

La página de propiedades **Certificados** agrega información sobre los certificados a la configuración del servicio. Tenga en cuenta que sus certificados no se incluyen con el servicio, debe cargarlos por separado en Azure a través del [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Para asociar un certificado a su rol, proporcione un nombre para el certificado. Usará este nombre para hacer referencia al certificado cuando configure un extremo HTTPS en la página de propiedades **Extremos**. A continuación, especifique si el almacén de certificados es **Local Machine** o **Current User**, así como el nombre del almacén Por último, especifique la huella digital del certificado. Si el certificado está en el almacén Current User\\Personal (My), para especificar la huella digital del certificado selecciónelo en una lista rellenada. Si se encuentra en otra ubicación, especifique el valor de la huella digital manualmente.

Al agregar un certificado del almacén de certificados, cualquier certificado intermedio se agrega automáticamente a la configuración. Estos certificados intermedios también se deben cargar en Azure para configurar el servicio correctamente para SSL.

Cualquier certificado de administración que asocie a su servicio solo se aplicará a su servicio cuando se ejecute en la nube. Cuando el servicio se ejecuta en el entorno de desarrollo local, usa un certificado estándar administrado por el emulador de proceso.

## Configuración del proyecto de servicio en la nube de Azure

Para configurar los valores que se aplican a todo un proyecto de servicio en la nube de Azure, primero abra el menú contextual para ese nodo de proyecto y elija Propiedades para abrir las páginas de propiedades. En la siguiente tabla se muestran las páginas de propiedades.

|Página de propiedades|Descripción|
|---|---|
|Application|Desde esta página, puede mostrar información acerca de la versión de Azure Tools que usa este proyecto de servicio en la nube, y puede actualizar a la versión actual de las herramientas.|
|Eventos de compilación|Desde esta página, puede establecer los eventos anteriores y posteriores a la compilación.|
|Desarrollo|Desde esta página, puede especificar las instrucciones de la configuración de compilación y las condiciones bajos las cuales se ejecutan los eventos posteriores a la compilación.|
|Web|Desde esta página, puede configurar los valores relacionados con el servidor web.|

<!---HONumber=AcomDC_0204_2016-->
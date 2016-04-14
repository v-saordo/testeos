<properties
	 pageTitle="Usar el portal de Azure para administrar el centro de IoT | Microsoft Azure"
	 description="Información general sobre cómo crear y administrar los centros de IoT de Azure a través del portal de Azure"
	 services="iot-hub"
	 documentationCenter=""
	 authors="nasing"
	 manager="timlt"
	 editor=""/>

<tags
	 ms.service="iot-hub"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.tgt_pltfrm="na"
	 ms.workload="na"
	 ms.date="10/19/2015"
	 ms.author="nasing"/>

# Administración de Centros de IoT a través del portal de Azure

## Introducción

En este artículo se describe cómo empezar a trabajar con Centros de IoT a través del Portal de Azure, cómo buscar Centros de IoT, y cómo crearlos y administrarlos.

## Dónde encontrar centros de IoT

Se pueden encontrar Centros de IoT en varios sitios.

1. **+ Nuevo**: el **Centro de IoT de Azure** es un servicio de IoT, que se puede encontrar en la categoría **Internet de las cosas** en **+ Nuevo**, al igual que otros servicios.

2. También se puede acceder a los Centros de IoT mediante Marketplace como servicio destacado en **Internet de las cosas**.

## Crear un centro de IoT

Puede crear un centro de IoT con los métodos siguientes.

1. Al crear un centro de IoT mediante la opción **+ Nuevo**, aparece la hoja que se muestra en la siguiente captura de pantalla. Los pasos para crear el Centro de IoT tanto con este método como con Marketplace son idénticos.

2. Para crear un Centro de IoT mediante Marketplace: al hacer clic en **Crear**, se abre una hoja igual a la hoja anterior de la experiencia **+ Nuevo**. Para crear un Centro de IoT, se deben seguir varios pasos que se enumeran en las secciones siguientes.

### Elección del nombre del Centro de IoT

Para crear un Centro de IoT, debe asignar un nombre al centro. Tenga en cuenta que este nombre debe ser único entre los centros. La duplicación de centros no está permitida en el back-end, por lo que se recomienda asignar a este centro un nombre lo más único posible.

### Elección del plan de tarifa

Puede elegir entre tres planes: **Gratis**, **Estándar 1 ** y **Estándar 2**. El plan gratis permite solo la conexión de 10 dispositivos con el Centro de IoT.

**S1 (baja frecuencia)**: la edición S1 (baja frecuencia) de Centros de IoT está diseñada para las soluciones de IoT que tienen un gran número de dispositivos que generan cantidades de datos relativamente pequeñas por cada dispositivo. Cada unidad de la edición S1 (baja frecuencia) permite la conectividad de hasta 500 dispositivos o hasta 50.000 mensajes por día en todos los dispositivos conectados.

**S2 (alta frecuencia)**: la edición S2 (alta frecuencia) de Centros de IoT está diseñada para las soluciones de IoT en las que los dispositivos generan grandes cantidades de datos. Cada unidad de la edición S2 (alta frecuencia) permite la conectividad de hasta 500 dispositivos o hasta 1,5 millones de mensajes por día entre todos los dispositivos conectados.

![][4]

> [AZURE.NOTE] El Centro de IoT solo permite un único centro gratuito por cada suscripción.

### Unidades del Centro de IoT

Una unidad de centro de IoT incluye 500 dispositivos, por lo que la elección del número de unidades de IoT significa que el número total de los dispositivos compatibles para este centro es el número de unidades multiplicado por 500. Por ejemplo, si desea que el Centro de IoT admita 1000 dispositivos, seleccione dos unidades.

### Dispositivo para particiones en la nube y grupo de recursos

Puede cambiar el número de particiones para un centro de IoT. Las particiones predeterminadas están establecidas en cuatro; sin embargo, puede elegir un número diferente de particiones en una lista desplegable.

Para los grupos de recursos, no es necesario crear explícitamente un grupo de recursos vacío. Al crear un nuevo recurso, puede elegir entre crear un nuevo grupo de recursos o usar un grupo de recursos existente.

![][5]

### Selección de suscripciones

El Centro de IoT de Azure muestra automáticamente la lista de suscripciones al que está vinculada la cuenta de usuario. Puede elegir una de estas opciones para asociar el Centro de IoT a esa suscripción.

### Selección de la ubicación

La opción de ubicación proporciona una lista de las regiones en las que se ofrece Centro de IoT. Centro de IoT está disponible para su implementación en las siguientes ubicaciones: Este de EE. UU., Oeste de EE. UU., Norte de Europa, Oeste de Europa, Asia Oriental y Asia Sudoriental.

### Creación del Centro de IoT

Cuando se completen todos los pasos anteriores, el centro de IoT estará listo para crearse. Haga clic en **Crear** para iniciar el proceso back-end de creación de este centro de IoT con las opciones específicas e implementarlo en la ubicación especificada.

Tenga en cuenta que la creación del Centro de IoT puede tardar unos minutos, ya que se necesita tiempo para que la implementación back-end se produzca en los servidores de ubicación adecuados.

## Cambio de la configuración del Centro de IoT

Puede cambiar la configuración de un Centro de IoT existente después de crearlo. Haga clic en el nombre del centro de IoT para abrir la página de configuración.

![][8]

**Directivas de acceso compartido**: se trata de las directivas que definen los permisos para que los dispositivos y servicios se conecten al Centro de IoT. Puede acceder a estas directivas haciendo clic en **Directivas de acceso compartido** de **Configuración**. En esta hoja puede modificar las directivas existentes o agregar una nueva.

### Creación de una nueva directiva

- Haga clic en **Agregar** para abrir una hoja en la que puede escribir el nombre de la nueva directiva y los permisos que quiere asociar a esta directiva, tal como se muestra en la siguiente ilustración.

	Hay varios permisos que se pueden asociar a estas directivas compartidas. Las dos primeras directivas, **Lectura del Registro** y **Escritura del Registro**, conceden derechos de acceso de lectura y escritura para el almacén de identidades de dispositivo o el registro de identidades. Tenga en cuenta que, si selecciona la opción de escritura, se elegirá automáticamente también la opción de lectura.

 	La directiva de conexión de servicios concede permiso al grupo de consumidores para servicios que se conectan al centro de IoT, mientras que la conexión de dispositivos concede permisos para los dispositivos del Centro de IoT.

- Haga clic en **Crear** para agregar la directiva recién creada a la lista existente.

![][10]

## Mensajería

Haga clic en las directivas de **Mensajería** a fin de mostrar una lista de propiedades de mensajería para el Centro de IoT que se está modificando. Hay dos tipos principales de propiedades que puede modificar o copiar: **De nube a dispositivo** y **De dispositivo a nube**.

- **Configuración de nube a dispositivo**: tiene dos opciones secundarias: **TTL de nube a dispositivo** (período de vida) y **Tiempo de retención** para los mensajes. Cuando se crea el Centro de IoT por primera vez, estas dos opciones se crean con un valor predeterminado de 1 hora. Sin embargo, puede personalizarlas mediante los controles deslizantes o escribir simplemente los valores.

- **Configuración de dispositivo a nube**: tiene varias opciones secundarias, algunas de las cuales se denominan/asignan al crear el Centro de IoT y solo se pueden copiar en otras opciones secundarias que son personalizables. Esta configuración se muestra en la siguiente sección.

**Particiones**: este valor se establece cuando se crea el Centro de IoT y se puede cambiar en esta configuración.

**Nombre compatible y extremo del Centro de eventos**: cuando se crea el Centro de IoT, se crea internamente un Centro de eventos al que el usuario puede necesitar acceder en determinadas circunstancias. Este nombre y extremo del Centro de eventos no se puede personalizar pero está disponible para su uso mediante el botón **Copiar**.

**Tiempo de retención**: establecido en 1 día de forma predeterminada pero se puede personalizar con otros valores en la lista desplegable. Tenga en cuenta que este valor es en días para Configuración de dispositivo a nube y no en horas, como en el caso de Configuración de nube a dispositivo.

**Grupos de consumidores**: Grupos de consumidores es una configuración similar a otros sistemas de mensajería que se puede usar para extraer datos de modos específicos a fin de conectar otras aplicaciones o servicios a un Centro de IoT. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, puede agregar o eliminar grupos de consumidores en los Centros de IoT.

> [AZURE.NOTE] El grupo de consumidores predeterminado no se puede modificar ni eliminar.

![][11]

## Precios y escala

El precio de un Centro de IoT existente se puede cambiar mediante la configuración **Precios**, con las siguientes excepciones:

- En la implementación actual, un Centro de IoT con una SKU gratuita no puede cambiar de nivel a una de las SKU de pago o viceversa.
- Solo puede haber un único Centro de IoT de nivel gratuito en la suscripción.

![][12]

Ir de un nivel alto (S2) a un nivel bajo (S1) solo está permitido cuando el número de mensajes enviados durante ese día no está en conflicto. Por ejemplo, si el número de mensajes por día supera los 50.000, no se puede cambiar el nivel para el centro de IoT de S2 a S1.

## Eliminación del Centro de IoT

Puede examinar el Centro de IoT que desea eliminar haciendo clic en **Examinar** y, a continuación, seleccionar el centro adecuado que desea eliminar. Haga clic en el botón **Eliminar** debajo del nombre del centro para eliminar el centro.

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción al Centro de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0204_2016-->
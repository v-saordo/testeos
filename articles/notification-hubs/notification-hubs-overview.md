<properties
	pageTitle="Centros de notificaciones de Azure"
	description="Aprenda a usar las notificaciones de inserción en Azure. Ejemplos de código escritos en C# con la API de .NET."
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="02/11/2016"
	ms.author="wesmc"/>


#Centros de notificaciones de Azure

##Información general

Los Centros de notificaciones de Azure son una infraestructura fácil de usar que le permite enviar notificaciones de inserción móviles desde cualquier back-end (en la nube o de forma local) a cualquier plataforma móvil.

Con los Centros de notificaciones puede enviar fácilmente notificaciones de inserción personalizadas entre plataformas, resumiendo los detalles de los distintos Sistemas de notificación de plataforma (PNS). Con una única llamada API, puede dirigirse a usuarios individuales o a segmentos completos con millones de usuarios, entre todos sus dispositivos.

Los Centros de notificaciones se pueden usar tanto para escenarios empresariales como de consumidores. Por ejemplo:

- Envíe notificaciones de noticias de última hora a millones de personas con baja latencia (los Centros de notificaciones posibilitan las aplicaciones de Bing instaladas previamente en todos los dispositivos Windows y Windows Phone).
- Envíe cupones basados en la ubicación a segmentos de usuarios.
- Envíe notificaciones de eventos a usuarios o grupos para aplicaciones deportivas, de finanzas o de juegos.
- Informe a los usuarios de eventos empresariales; por ejemplo, si tienen mensajes o correos electrónicos nuevos, o clientes potenciales.
- Envíe contraseñas únicas necesarias para la autenticación multifactor.



##¿Qué son las notificaciones de inserción?

Los smartphones y las tabletas cuentan con la posibilidad de "notificar" a los usuarios cuando se ha producido un evento. Estas notificaciones pueden adoptar muchas formas.

En las aplicaciones de la Tienda Windows y de Windows Phone, la notificación puede adoptar la forma de una _notificación del sistema_: una ventana sin modo que aparece, sin sonido, para indicar una nueva notificación. Se admiten otros tipos de notificación, como notificaciones de tipo _icono_, _sistema_ y _distintivo_. Para obtener más información sobre los tipos de notificaciones que se admiten, consulte [Mosaicos, distintivos y notificaciones](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

En los dispositivos con iOS de Apple, la notificación de inserción notifica manera similar con un cuadro de diálogo, que solicita al usuario que vea o cierre la notificación. Un clic en **Ver** abre la aplicación que está recibiendo el mensaje. Para obtener más información sobre las notificaciones de iOS, consulte [Notificaciones de iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

Las notificaciones de inserción ayudan a que los dispositivos móviles muestren información actualizada mientras ahorran energía. Las notificaciones pueden enviarse por sistemas de back-end a los dispositivos móviles, incluso cuando las aplicaciones correspondientes en un dispositivo no están activas. Las notificaciones de inserción son un componente esencial de las aplicaciones de consumidor, donde se utilizan para aumentar el uso y la interacción con la aplicación. Las notificaciones también son útiles para las empresas cuando la información actualizada aumenta la capacidad de respuesta de los empleados ante eventos empresariales.

Algunos ejemplos específicos de escenarios de interacción móvil son:

1.  Actualización de un icono en Windows 8 o Windows Phone con información financiera actual.
2.  Alerta con una notificación del sistema a un usuario cuando se le ha asignado algún elemento de trabajo en una aplicación empresarial basada en el flujo de trabajo.
3.  Visualización de un distintivo con el número de clientes potenciales actuales en una aplicación CRM (como Microsoft Dynamics CRM).

##Funcionamiento de las notificaciones de inserción

Las notificaciones de inserción se entregan a través de infraestructuras específicas para la plataforma llamadas _Sistemas de notificación de plataforma_ (PNS). Un PNS ofrece funciones estrictamente esenciales (es decir, sin compatibilidad para difusión, personalización) y no tiene una interfaz común. Por ejemplo, para enviar una notificación a una aplicación de la Tienda Windows, un desarrollador debe ponerse en contacto con el Servicio de notificaciones de Windows (WNS) para enviar una notificación a un dispositivo iOS; luego, el mismo desarrollador debe ponerse en contacto con el Servicio de notificaciones push de Apple (APNS) y volver a enviar el mensaje. Los Centros de notificaciones de Azure ayudan a proporcionar una interfaz común, junto con otras características para admitir las notificaciones push en cada plataforma.

Sin embargo, en un alto nivel, todos los sistemas de notificación de plataforma siguen el mismo patrón:

1.  La aplicación cliente se pone en contacto con el PNS para recuperar el _identificador_. El tipo de identificador depende del sistema. En el caso de WNS, es un URI o un "canal de notificaciones". En el caso de APNS, es un token.
2.  La aplicación cliente almacena este identificador en el _back-end_de la aplicación para usarlo más adelante. En el caso de WNS, el back-end normalmente es un servicio en la nube. En el caso de Apple, el sistema se llama _proveedor_.
3.  Para enviar una notificación de inserción, el back-end de la aplicación se pone en contacto con el PNS a través del identificador para dirigirse a una instancia de aplicación cliente específica.
4.  El PNS reenvía la notificación al dispositivo que especifica el identificador.

![][0]

##Los desafíos de las notificaciones de inserción

A pesar de que estos sistemas son muy potentes, de todos modos dejan mucho trabajo al desarrollador de aplicaciones para poder implementar incluso escenarios comunes de notificaciones de inserción, como la difusión o el envío de notificaciones de inserción a usuarios segmentados.

Las notificaciones de inserción son una de las características más solicitadas en los servicios en la nube para las aplicaciones móviles. El motivo es que la infraestructura que se requiere para hacerlas funcionar es muy complejo y, en gran parte, no está relacionada con la lógica de negocios principal de la aplicación. Algunas de las dificultades que presenta la creación de una infraestructura de inserción a petición son:

- **Dependencia de la plataforma.** A fin de enviar notificaciones a dispositivos en distintas plataformas, se deben codificar varias interfaces en el back-end. No solo son distintos los detalles a bajo nivel, sino que la presentación de la notificación (icono, notificación del sistema o distintivo) también depende de la aplicación. Estas diferencias pueden llevar a un código de back-end complejo y difícil de mantener.

- **Escala.** Escalar esta infraestructura tiene dos aspectos:
	+ Según las directrices de PNS, se deben actualizar los tokens de dispositivo cada vez que se inicia la aplicación. Esto genera una gran cantidad de tráfico (y los consecuentes accesos a la base de datos) solo para mantener actualizados los tokens de dispositivo. Cuando la cantidad de dispositivos crece (posiblemente a millones), no es posible pasar por alto el costo de crear y mantener esta infraestructura.

	+ La mayoría de los PNS no son compatibles con la difusión a varios dispositivos. Más allá de eso, la difusión a millones de dispositivos resulta en millones de llamadas a los PNS. La capacidad de escalar estas solicitudes no es algo trivial, porque normalmente los desarrolladores de aplicaciones desean mantener baja la latencia total (por ejemplo, el último dispositivo que recibe el mensaje no debería recibir la notificación 30 minutos después de enviadas las notificaciones, porque en muchos casos eso iría en contra del propósito de las propias notificaciones de inserción).
- **Enrutamiento.** Los PNS brindan una forma de enviar un mensaje a un dispositivo. Sin embargo, en la mayoría de las aplicaciones, las notificaciones se dirigen a usuarios y/o grupos de interés (por ejemplo, todos los empleados asignados a cierta cuenta de cliente). De tal modo, a fin de enrutar las notificaciones a los dispositivos correctos, el back-end de la aplicación debe mantener un registro que asocie grupos de interés con tokens de dispositivo. Esta sobrecarga se agrega al tiempo plazo de comercialización total y a los costos de mantenimiento de una aplicación.

##¿Por qué usar los Centros de notificaciones?

Los Centros de notificaciones eliminan la complejidad: ya no es necesario administrar los desafíos de las notificaciones push. En su lugar, puede utilizar un Centro de notificaciones. Los Centros de notificaciones usan una infraestructura completa de notificaciones de inserción de varias plataformas y con escalamiento horizontal, además de reducir considerablemente el código específico de inserción que se ejecuta en el back-end de la aplicación. Centros de notificaciones implementan toda la funcionalidad de una infraestructura de inserción. Los dispositivos solo son responsables de registrar identificadores de PNS, mientras que el back-end es responsable de enviar mensajes independientemente de la plataforma a usuarios o grupos de interés, tal como se muestra en la ilustración siguiente.

![][1]


Los Centros de notificaciones proporcionan una infraestructura de notificaciones push lista para usar con las ventajas siguientes:

- **Varias plataformas.**
	+  Compatibilidad con las principales plataformas móviles. Los centros de notificaciones pueden enviar notificaciones de inserción a aplicaciones de la Tienda Windows, iOS, Android y Windows Phone.

	+  Los centros de notificaciones proporcionan una interfaz común para enviar notificaciones a todas las plataformas compatibles. No se requieren protocolos específicos de la plataforma. El back-end de la aplicación puede enviar notificaciones en formatos específicos para una plataforma o independientes de la plataforma. La aplicación solo se comunica con los Centros de notificaciones.

	+  Administración de controladores de dispositivos. Los centros de notificaciones se encargan del mantenimiento de los comentarios y del registro de identificadores de los PNS.

- **Funciona con todos los back-ends**: en la nube o locales,. NET, PHP, Java, Node, etc.

- **Escala.** Los centros de notificaciones escalan hasta millones de dispositivos sin tener que volver a diseñar o particionar.


- **Conjunto completo de patrones de entrega**:

	- *Difusión*: permite la difusión casi simultánea a millones de dispositivos con una sola llamada API.

	- *Unidifusión/multidifusión*: inserción en etiquetas que representan usuarios concretos, incluidos todos sus dispositivos; o con grupos más amplios como, por ejemplo, factores de forma independientes (tableta o teléfono).

	- *Segmentación*:inserción en segmentos complejos definidos por expresiones de etiquetas (por ejemplo, dispositivos de Sevilla que siguen al Betis).

	Cada dispositivo, cuando envía su identificador a un centro de notificaciones, puede especificar una o más _etiquetas_. Para obtener más información acerca las [etiquetas]. Las etiquetas no deben ser aprovisionadas previamente ni eliminadas. Las etiquetas brindan una manera simple de enviar notificaciones a usuarios o grupos de interés. Como las etiquetas pueden contener cualquier identificador específico para una aplicación (como identificadores de usuario o grupo), al usarlas se libera al back-end de la aplicación de la carga de tener que almacenar y administrar identificadores de dispositivo.

- **Personalización**: cada dispositivo puede tener una o más plantillas para lograr la localización o la personalización dispositivo a dispositivo sin que el código del back-end se vea afectado.

- **Seguridad**: firma de acceso compartido (SAS) o autenticación federada.

- **Telemetría completa**: disponible en el portal y mediante programación.


##Integración con las Aplicaciones móviles del Servicio de aplicaciones

Para facilitar una experiencia perfecta y unificadora en servicios de Azure, [Aplicaciones móviles del Servicio de aplicaciones] tiene compatibilidad integrada para notificaciones push con centros de notificaciones. Las [Aplicaciones móviles del Servicio de aplicaciones] ofrecen una plataforma de desarrollo de aplicaciones móviles altamente escalable y disponible globalmente para desarrolladores empresariales e integradores de sistemas que proporciona un amplio conjunto de funcionalidades a desarrolladores móviles.

Los desarrolladores de aplicaciones móviles pueden usar centros de notificaciones con el siguiente flujo de trabajo:

1. Recuperar controlador PNS de dispositivo
2. Registrar el dispositivo y las [plantillas] con los centros de notificaciones a través de la API adecuada del registro del SDK de cliente de aplicaciones móviles
    + Tenga en cuenta que las aplicaciones móviles eliminan todas las etiquetas en los registros por motivos de seguridad. Trabaje con centros de notificaciones desde su back-end directamente para asociar etiquetas a dispositivos.
3. Enviar notificaciones desde su back-end de aplicación con los centros de notificaciones

Estas son algunas ventajas para los desarrolladores de esta integración:
- **SDK de cliente de Aplicaciones móviles.** Estos SDK de multiplataforma ofrecen API simples para el registro y se comunican con el centro de notificaciones vinculado con la aplicación móvil automáticamente. Los desarrolladores no tienen que indagar en las credenciales de los Centros de notificaciones y trabajar con un servicio adicional.
    + Los SDK etiquetan automáticamente el dispositivo específico con un identificador de usuario autenticado de Aplicaciones móviles para habilitar la inserción en un escenario de usuario.
    + Los SDK utilizan automáticamente el identificador de instalación de Aplicaciones móviles como GUID para registrarse con Centros de notificaciones, lo que permite que los desarrolladores no tengan que mantener varios GUID de servicio.
- **Modelo de instalación.** Aplicaciones móviles funciona con el modelo de inserción más reciente de los Centros de notificaciones para representar todas las propiedades de inserción asociadas a un dispositivo en una instalación de JSON que se alinea con los servicios de notificaciones push y resulta fácil de usar.
- **Flexibilidad.** Los desarrolladores siempre pueden elegir trabajar con los Centros de notificaciones directamente, incluso con la integración implementada.
- **Experiencia en integrada en el [Portal de Azure].** La inserción como capacidad se representa visualmente en Aplicaciones móviles y los desarrolladores pueden trabajar fácilmente con el centro de notificaciones asociado a través de Aplicaciones móviles.



##Pasos siguientes

Obtenga más información acerca de los Centros de notificaciones en estos temas:

+ **[Cómo utilizan los clientes los Centros de notificaciones]**

+ **[Tutoriales y guías sobre los Centros de notificaciones]**

+ **Tutoriales de introducción sobre los Centros de notificaciones** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Las referencias pertinentes para la API administrada de .NET referidas a las notificaciones de inserción se pueden encontrar en los siguientes temas:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Cómo utilizan los clientes los Centros de notificaciones]: http://azure.microsoft.com/services/notification-hubs
  [Tutoriales y guías sobre los Centros de notificaciones]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicaciones móviles del Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/documentation/articles/app-service-mobile-value-prop/
  [plantillas]: notification-hubs-templates.md
  [Portal de Azure]: https://portal.azure.com
  [etiquetas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0309_2016-->
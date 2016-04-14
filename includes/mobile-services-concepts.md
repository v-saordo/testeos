## <a name="what-is"></a>Qué es Servicios móviles

Servicios móviles de Azure es una plataforma para el desarrollo de aplicaciones móviles altamente escalable que le permite agregar funcionalidad mejorada a las aplicaciones para dispositivos móviles mediante el uso de Azure.

Con Servicios móviles puede:

+ **Compilar aplicaciones nativas y multiplataforma**: conecte sus aplicaciones Xamarin o Cordova (Phonegap) de iOS, Android, Windows o multiplataforma con su servicio móvil de backend mediante el uso de SDK nativos.  
+ **Enviar notificaciones push a sus usuarios**: envíe notificaciones push a los usuarios de su aplicación.
+ **Autenticar sus usuarios**: aproveche proveedores de identidades conocidos, como Facebook y Twitter, para autenticar a los usuarios de su aplicación.
+ **Almacenar datos en la nube**: almacene datos de usuario en una Base de datos SQL (de manera predeterminada) o en Mongo DB, DocumentDB, tablas de Azure o blobs de Azure. 
+ **Compilar aplicaciones de uso sin conexión con sincronización**: haga que sus aplicaciones funcionen sin conexión y use Servicios móviles para sincronizar datos en segundo plano.
+ **Supervisar y escalar las aplicaciones**: supervise el uso de las aplicaciones y escale el backend a medida que aumenta la demanda. 

## <a name="concepts"> </a>Conceptos de Servicios móviles

A continuación se presentan características y conceptos importantes en los Servicios móviles:

+ **Clave de aplicación:** un valor único que se usa para limitar el acceso de clientes aleatorios a su servicio móvil; esta "clave" no es un token de seguridad y no se usa para autenticar usuarios de la aplicación.    
+ **Backend:** la instancia de servicio móvil que admite su aplicación. Un servicio móvil se implementa como un proyecto ASP.NET Web API (*back-end de .NET*) o como un proyecto de Node.js (*back-end de JavaScript*).
+ **Proveedor de identidades:** un servicio externo, con la confianza de Servicios móviles, que autentica los usuarios de su aplicación. Los proveedores compatibles incluyen: Facebook, Twitter, Google, cuenta de Microsoft y Azure Active Directory. 
+ **Notificación de inserción:** mensaje iniciado por el servicio que se envía a un dispositivo o usuario registrados mediante los centros de notificación de Azure.
+ **Escala:** la capacidad de agregar, por un coste adicional, más potencia de procesamiento, rendimiento y almacenamiento a medida que su aplicación se hace más conocida.
+ **Trabajo programado:** código personalizado que se ejecuta en una programación predeterminada o a petición.

Para obtener más información, consulte [Conceptos de Servicios móviles](mobile-services-concepts-links.md).

<!---HONumber=Nov15_HO2-->
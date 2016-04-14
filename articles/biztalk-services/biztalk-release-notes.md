<properties 
	pageTitle="Notas de la versión de los Servicios de BizTalk de Azure | Servicios de BizTalk de Microsoft Azure" 
	description="" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="msftman" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="deonhe"/>

# Notas de la versión de Servicios de BizTalk de Azure

Las notas de la versión de los Servicios de BizTalk de Microsoft Azure contienen los problemas conocidos de esta versión.

## Novedades de la actualización de noviembre de Servicios de BizTalk
* El cifrado en reposo se puede habilitar en el Portal de Servicios de BizTalk. Consulte [Habilitación del cifrado en reposo en el Portal de Servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## Historial de actualizaciones

### Actualización de octubre

* Se admiten cuentas de la organización:  
 * **Escenario**: ha registrado una implementación de un servicio de BizTalk mediante una cuenta Microsoft (como user@live.com). En este escenario, solo los usuarios de cuentas Microsoft pueden administrar el servicio de BizTalk mediante el Portal de Servicios de BizTalk. No se puede usar una cuenta de la organización.  
 * **Escenario**: ha registrado una implementación de un servicio de BizTalk con una cuenta de la organización en una instancia de Azure Active Directory (como user@fabrikam.com o user@contoso.com). En este escenario, solo los usuarios de Azure Active Directory de la misma organización pueden administrar el servicio de BizTalk mediante el Portal de Servicios de BizTalk. No se puede usar una cuenta Microsoft.  
* Cuando se crea un servicio de BizTalk en el Portal de Azure clásico, se registra automáticamente en el Portal de Servicios de BizTalk.
 * **Escenario**: inicia sesión en el Portal de Azure clásico, crea un servicio de BizTalk y luego selecciona **Administrar** por primera vez. Cuando se abre el Portal de Servicios de BizTalk, el servicio de BizTalk se registra automáticamente y está listo para sus implementaciones. Consulte [Registrar y actualizar una implementación del servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### Actualización del 14 de agosto
* Desacoplamiento de contratos y puentes. Ahora, los contratos y los puentes entre socios comerciales están desacoplados en el Portal de Servicios de BizTalk. Los contratos y los puentes se crean ahora por separado, y en tiempo de ejecución los puentes se resuelven en un contrato basado en los valores del mensaje de intercambio electrónico de datos (EDI). Consulte [Crear acuerdos en Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Crear un puente EDI mediante el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Creación de un puente AS2 mediante el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx) y [¿Cómo resuelven los puentes acuerdos en tiempo de ejecución?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* La opción para crear plantillas de contratos ya no está disponible.  
* Para el contrato de envío, ahora puede especificar distintos conjuntos de delimitadores para cada esquema. Esta configuración se especifica en la configuración del protocolo del contrato de envío. Para más información, consulte [Crear un acuerdo X12 en los servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) y [Crear un acuerdo EDIFACT en Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/dn606267.aspx). También se agregan dos nuevas entidades a la API de OM de TPM con la misma finalidad. Consulte [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) y [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Ahora se admiten construcciones XSD estándar, incluidos los tipos derivados. Consulte [Uso de construcciones XSD estándar en las asignaciones](https://msdn.microsoft.com/library/azure/dn793987.aspx) y [Uso de tipos derivados en escenarios y ejemplos de asignaciones](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 admite nuevos algoritmos MIC para la firma del mensaje y nuevos algoritmos de cifrado. Consulte [Crear un contrato AS2 en Servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## Problemas conocidos

### Problemas de conectividad después de la actualización del Portal de Servicios de BizTalk

  Si tiene abierto el Portal de Servicios de BizTalk mientras se actualizan los Servicios de BizTalk para implementar los cambios en el servicio, podría experimentar problemas de conectividad con dicho portal.  
  Como solución alternativa, puede reiniciar el explorador, eliminar la memoria caché del explorador o iniciar el portal en modo privado.

### El IDE de Visual Studio no puede encontrar el artefacto si hace clic en un error o una advertencia en un proyecto de Servicios de BizTalk
Instale Visual Studio 2012 Update 3 RC 1 para corregir el problema.

### Referencia al proyecto de enlace personalizado
Tenga en cuenta las siguientes situaciones con un proyecto de Servicios de BizTalk en una solución de Visual Studio:
* En la misma solución de Visual Studio, hay un proyecto de Servicios de BizTalk y un proyecto de enlace personalizado. El proyecto de Servicios de BizTalk tiene una referencia a este archivo de proyecto de enlace personalizado. 
* El proyecto de Servicios de BizTalk tiene una referencia a una DLL de comportamiento o enlace personalizado.

Compila la solución en Visual Studio correctamente. A continuación, recompila o limpia la solución. Después de eso, al recompilar o limpiar de nuevo, se produce el siguiente error:  
  No se puede copiar el archivo <Path to DLL> en "bin\\Debug\\FileName.dll". El proceso no puede obtener acceso al archivo "bin\\Debug\\FileName.dll" porque está siendo usado en otro proceso.

#### Solución alternativa
* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) está instalado, tiene las dos opciones siguientes:

  * Reiniciar Visual Studio, o

  * Reiniciar la solución. A continuación, realice una sola compilación en la solución.

* Si [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) no está instalado, abra el Administrador de tareas, haga clic en la pestaña Procesos, haga clic en el proceso MSBuild.exe y, luego, haga clic en el botón Finalizar proceso.

### No se admite el enrutamiento a los puntos de conexión de BasicHttpRelay desde los puentes y el Portal de Servicios de BizTalk si los caracteres no imprimibles se promueven como encabezados HTTP

Si usa caracteres no imprimibles como parte de las propiedades promocionadas en los mensajes, dichos mensajes no se podrán enrutar a destinos de retransmisión que utilicen el enlace BasicHttpRelay. Además, las propiedades promocionadas que están disponibles como parte del seguimiento están codificadas en URL para blobs y no codificadas para destinos.

### MDN se envía de forma asincrónica incluso si no está activada la opción Enviar MDN asincrónico  
Considere este escenario: si activa la casilla **Enviar MDN asincrónico** y especifica una dirección URL a la que enviar el MDN asincrónico y luego desactiva de nuevo ** esta casilla**, el MDN se seguirá enviando a la dirección URL especificada aunque no esté activada la opción para enviar MDN asincrónico. Como solución alternativa, debe borrar la dirección URL especificada antes de desactivar la casilla **Enviar MDN asincrónico** y, luego, implementar el contrato AS2.

### Los caracteres de espacio en blanco después de un intercambio válido hacen que se envíe un mensaje vacío al punto de conexión de suspensión.  
Si hay espacios en blanco después de un segmento de IEA, el desensamblador lo trata como extremo del intercambio actual y examina el siguiente conjunto de espacios en blanco como un mensaje nuevo. Puesto que no es un intercambio válido, puede observar que se envía un mensaje correcto al destino de ruta y un mensaje vacío al punto de conexión de suspensión.
### Seguimiento en el Portal de Servicios de BizTalk  
Los eventos de seguimiento se capturan hasta el procesamiento de mensaje EDI y las posibles correlaciones. Si se produce un error en un mensaje fuera de la fase de protocolo, el seguimiento se muestra como correcto. En esta situación, consulte la sección de registro de la columna **Detalles** de **Seguimiento** para información detallada del error. 
En la configuración de envío y recepción de X 12 ([Crear un acuerdo X12 en los servicios de BizTalk de Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) se proporciona información sobre la fase de protocolo.

### Actualización del contrato  
El Portal de Servicios de BizTalk le permite modificar el calificador de una identidad cuando se configura un contrato. Esto puede dar lugar a propiedades de incoherencia. Por ejemplo, hay un contrato que usa ZZ: 1234567 y ZZ: 7654321 como calificador. En la configuración de perfil del Portal de Servicios de BizTalk, cambia ZZ:1234567 por 01:ChangedValue. Abre el contrato y se muestra 01:ChangedValue en lugar de ZZ:1234567.
Para modificar el calificador de una identidad, elimine el contrato, actualice las **identidades** en el perfil de socio y luego vuelva a crear el contrato.
> AZURE.WARNING Este comportamiento afecta a X12 y AS2.

### Datos adjuntos de AS2  
Los datos adjuntos de AS2 no se admiten en el envío o la recepción. En concreto, los datos adjuntos se ignoran sin notificaciones y el cuerpo del mensaje se procesa como un mensaje AS2 normal.
### Recursos: recuerdo de la ruta de acceso  
Al agregar **recursos**, es posible que la ventana de diálogo no recuerde la ruta de acceso usada previamente para agregar un recurso. Para recordar la ruta de acceso usada anteriormente, pruebe a agregar el sitio web del Portal de Servicios de BizTalk a **Sitios de confianza** en Internet Explorer.
### Si cambia el nombre de la entidad de un puente y cierra el proyecto sin guardar los cambios, al volver a abrir la entidad se produce un error
Considere un escenario en el orden siguiente:  
* Agregue un puente (por ejemplo, un puente unidireccional XML) a un proyecto de Servicios de BizTalk.  

* Cambia el nombre del puente mediante la especificación de un valor para la propiedad Nombre de entidad. De esta manera se cambia el nombre del archivo .bridgeconfig asociado con el nombre especificado.

* Cierra el archivo .bcs (cerrando la pestaña en Visual Studio) sin guardar los cambios.

* Abre de nuevo el archivo .bcs desde el Explorador de soluciones.
Observará que mientras que el archivo .bridgeconfig asociado tiene el nuevo nombre especificado, el nombre de entidad en la superficie de diseño sigue siendo el antiguo. Si intenta abrir la configuración del puente haciendo doble clic en el componente de puente, obtendrá el siguiente error:  
  '<old name>'El archivo asociado de la entidad'<old name>.bridgeconfig' no existe.  
Para evitar esta situación, asegúrese de guardar los cambios después de cambiar el nombre de las entidades en un proyecto de Servicios de BizTalk.
### El proyecto de Servicios de BizTalk se compila correctamente incluso si un artefacto se ha excluido de un proyecto de Visual Studio
Considere un escenario donde se agrega un artefacto (por ejemplo, un archivo XSD) a un proyecto de Servicios de BizTalk. Incluye ese artefacto en la configuración del puente (por ejemplo, especificándolo como un tipo de mensaje de solicitud) y luego lo excluye del proyecto de Visual Studio. En tal caso, la compilación del proyecto no dará ningún error siempre y cuando el artefacto eliminado esté disponible en el disco en la misma ubicación desde la que se incluyó en el proyecto de Visual Studio.
### El proyecto de Servicios de BizTalk no comprueba la disponibilidad del esquema mientras se configuran los puentes
En un proyecto de Servicios de BizTalk, si un esquema que se agrega al proyecto importa otro esquema, dicho proyecto no comprueba si el esquema importado se agrega al proyecto. Si intenta compilar este proyecto, no recibirá ningún error de compilación.
### El mensaje de respuesta para un puente de solicitud y respuesta XML es siempre del juego de caracteres UTF-8
En esta versión, el juego de caracteres del mensaje de respuesta de un puente de solicitud y respuesta XML siempre se establece en UTF-8.
### Tipos de datos definidos por el usuario
Los adaptadores de BizTalk Adapter Pack dentro de la característica de servicios BizTalk Adapter pueden usar tipos de datos definidos por el usuario para las operaciones del adaptador. Al usar tipos de datos definidos por el usuario, copie los archivos (.dll) en la unidad:\\Program Files\\Microsoft BizTalk Adapter Service\\BAServiceRuntime\\bin\\ o en la caché global de ensamblados (GAC) en el servidor que hospeda los servicios de BizTalk Adapter. De lo contrario, puede producirse el siguiente error en el cliente:
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="es-ES">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Se recomienda usar GACUtil.exe para instalar un archivo en la caché global de ensamblados. En GACUtil.exe se documenta el uso de esta herramienta y las opciones de línea de comandos de Visual Studio.

### Reinicio del sitio web de servicios BizTalk Adapter
Al instalar el **tiempo de ejecución de servicios BizTalk Adapter*** se crea el sitio web de **servicios BizTalk Adapter** en IIS que contiene la aplicación **BAService**. La aplicación **BAService** usa internamente el enlace de retransmisión para ampliar el alcance del punto de conexión de servicio local a la nube. En un servicio hospedado local, el punto de conexión de retransmisión correspondiente se registrará en el Bus de servicio solamente cuando se inicia el servicio local.

Si detiene e inicia una aplicación, no se respeta su configuración de inicio automático. Así que cuando se detenga **BAService**, siempre debe reiniciar en su lugar el sitio web de **servicios BizTalk Adapter**. No inicie ni detenga la aplicación **BAService**.
### No deben usarse caracteres especiales en nombres de direcciones y entidades de componentes LOB
No debe usar caracteres especiales en nombres de direcciones y entidades de componentes LOB. Si lo hace, obtendrá un error al implementar el proyecto de servicio de BizTalk. En el caso de algunos caracteres como '%', el sitio web de servicios BizTalk Adapter puede entrar en un estado detenido y tendrá que iniciarlo manualmente.
### Prueba de asignación con la propiedad de obtención de contexto
Si una transformación contiene una operación de asignación de **propiedad de obtención de contexto**, la **comprobación de asignación** producirá un error. Como solución temporal, reemplace la operación de asignación de **propiedad de obtención de contexto** por una operación de asignación de concatenación de cadenas que contiene datos ficticios. De esta forma se llenará el esquema de destino y podrá probar otra función de transformación.
### No se muestra la propiedad Comprobar asignación
Las propiedades **Comprobar asignación** no se muestran en Visual Studio. Esto puede ocurrir si la ventana **Propiedades** y la ventana **Explorador de soluciones** no se acoplan simultáneamente. Para resolver este problema, acople las ventanas **Propiedades** y **Explorador de soluciones**.
### La lista desplegable para volver a aplicar formato de fecha y hora está atenuada
Cuando se agrega una operación de asignación para volver a aplicar formato de fecha y hora a la superficie de diseño y se configura, la lista desplegable de formato puede aparecer atenuada. Esto puede ocurrir si la pantalla del equipo está establecida en **Mediano: 125%** o **Más grande: 150%**. Para resolver este problema, realice los pasos siguientes para configurar la pantalla como **Más pequeño: 100% (predeterminado)**:  
1. Abra el **Panel de Control** y haga clic en **Apariencia y personalización**.
2. Haga clic en **Pantalla**.
3. Haga clic en **Más pequeño: 100% (predeterminado)** y haga clic en **Aplicar**.

La lista desplegable **Formato** debe funcionar ahora según lo esperado.
### Contratos duplicados en el Portal de Servicios de BizTalk
Considere el siguiente escenario:
1. Crea un contrato mediante la API de OM de administración de socios comerciales.
2. Abre el contrato en el Portal de Servicios de BizTalk en dos pestañas diferentes.
3. Implementa el contrato desde ambas pestañas.
4. Como resultado, la implementación de ambos contratos da lugar a entradas duplicadas en el Portal de Servicios de BizTalk.

**Solución alternativa**. Abra uno de los contratos duplicados en el Portal de Servicios de BizTalk y anule la implementación.

### Los puentes no usan certificados actualizados incluso después de que se ha actualizado un certificado en el almacén de artefactos.
Considere los siguientes escenarios:

**Escenario 1: Uso de certificados basados en huella digital para garantizar la transferencia de mensajes de un puente a un punto de conexión de servicio**  
Considere un escenario donde usa certificados basados en huella digital en el proyecto de servicio de BizTalk. Actualiza el certificado en el Portal de Servicios de BizTalk con el mismo nombre pero con una huella digital diferente, pero no actualiza el proyecto de servicio de BizTalk de acuerdo con esa modificación. En este escenario, el puente puede seguir procesando los mensajes porque es posible que los datos de certificado más antiguos estén aún en la memoria caché del canal. Después de eso, el procesamiento de mensajes da error.

**Solución alternativa**: actualice el certificado en el proyecto de servicio de BizTalk y vuelva a implementar el proyecto.

**Escenario 2: Uso de comportamientos basados en el nombre para identificar certificados a fin de proteger la transferencia de mensajes desde un puente hasta un punto de conexión de servicio** 

Considere un escenario donde usa comportamientos basados en el nombre para identificar certificados en el proyecto de servicio de BizTalk. Actualiza el certificado en el Portal de Servicios de BizTalk, pero no actualiza el proyecto de servicio de BizTalk de acuerdo con esta modificación. En este escenario, el puente puede seguir procesando los mensajes porque es posible que los datos de certificado más antiguos estén aún en la memoria caché del canal. Después de eso, el procesamiento de mensajes da error.

**Solución alternativa**: actualice el certificado en el proyecto de servicio de BizTalk y vuelva a implementar el proyecto.

### Los puentes siguen procesando los mensajes aunque la Base de datos SQL esté sin conexión
Los puentes de Servicios de BizTalk continúan procesando los mensajes durante un tiempo, aunque la Base de datos SQL de Microsoft Azure (que almacena la información de ejecución como artefactos y canalizaciones implementados) esté sin conexión. El motivo es que los Servicios de BizTalk usan la configuración de puentes y artefactos almacenada en caché. 
Si no desea que los puentes procesen mensajes cuando la Base de datos SQL está sin conexión, puede usar los cmdlets de PowerShell de Servicios de BizTalk para detener o suspender el servicio de BizTalk. Consulte el ejemplo de código [Azure BizTalk Service Management](http://go.microsoft.com/fwlink/p/?LinkID=329019) para conocer los cmdlets de Windows PowerShell para administrar operaciones.
### La lectura del mensaje XML en el componente de código personalizado de un puente incluye un carácter de marca de orden de byte (BOM) adicional
Considere un escenario donde quiere leer un mensaje XML dentro del código personalizado de un puente. Si usa la API de .NET System.Text.Encoding.UTF8.GetString(bytes), se incluye un carácter BOM adicional en la salida al comienzo del mensaje. Por lo tanto, si no quiere que la salida incluya dicho carácter, debe usar ```System.IO.StreamReader().ReadToEnd()```.
### El envío de mensajes a un puente con WCF no se escala
Los mensajes enviados a un puente con WCF no se escalan. Si quiere un cliente escalable, debe usar en su lugar HttpWebRequest.
### ACTUALIZACIÓN: error del proveedor de tokens después de actualizar de la versión preliminar de Servicios de BizTalk a disponibilidad general (GA)
Existe un contrato EDI o AS2 con lotes activos. Cuando Servicios de BizTalk se actualiza de versión preliminar a disponibilidad general, puede ocurrir lo siguiente:
* Error: el proveedor de tokens no pudo proporcionar un token de seguridad. El proveedor de tokens devolvió el mensaje: no se puede resolver el nombre remoto.

* Las tareas de Lote se cancelan.

**Solución**: después de que el servicio de BizTalk se actualice a disponibilidad general (GA), vuelva a implementar el contrato.

### ACTUALIZACIÓN: el cuadro de herramientas muestra los iconos de puente antiguos después de actualizar el SDK de Servicios de BizTalk
Después de actualizar una versión anterior del SDK de Servicios de BizTalk, que tenía iconos antiguos que representaban los puentes, el cuadro de herramientas sigue mostrando estos iconos. Sin embargo, si agrega un puente a la superficie de diseño del proyecto de servicio de BizTalk, la superficie muestra el nuevo icono.

**Solución alternativa**. Puede solucionar este problema mediante la eliminación de los archivos .tbd situados en <system drive>:\\Users<usuario>\\AppData\\Local\\Microsoft\\VisualStudio\\11.0.

### ACTUALIZACIÓN: la actualización del Portal de BizTalk de la versión preliminar a la versión de disponibilidad general podría mostrar un error que indica que la función EDI no se encuentra disponible
Si inicia sesión en el Portal de Servicios de BizTalk mientras los Servicios de BizTalk se actualizan de la versión preliminar a la versión de disponibilidad general, puede que reciba el siguiente error en el portal:

Esta función no se encuentra disponible como parte de esta edición de Servicios de BizTalk de Microsoft Azure. Para usar estas funciones, cambie a una edición adecuada.

**Resolución**: cierre sesión en el portal, cierre y abra el explorador y, a continuación, inicie sesión en el portal.
### ACTUALIZACIÓN: los nuevos datos de seguimiento no se muestran después de actualizar los Servicios de BizTalk a la versión de disponibilidad general
Supongamos un escenario donde ha implementado un puente XML en la suscripción de la versión preliminar de Servicios de BizTalk. Envía mensajes al puente y los datos de seguimiento correspondientes están disponibles en el Portal de Servicios de BizTalk. Ahora, si los bits de tiempo de ejecución del Portal de Servicios de BizTalk y de los Servicios de BizTalk se actualizan a la versión de disponibilidad general, y envía un mensaje al mismo punto de conexión de puente implementado anteriormente, los datos de seguimiento no muestran los mensajes enviados después de la actualización.

### Canalizaciones frente a puentes
En este documento los términos "canalizaciones" y "puentes" se usan indistintamente. Básicamente, ambos significan lo mismo, es decir, una unidad de procesamiento de mensajes implementada en los Servicios de BizTalk.

### Conceptos  

[Servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)

<!---HONumber=AcomDC_0302_2016-->



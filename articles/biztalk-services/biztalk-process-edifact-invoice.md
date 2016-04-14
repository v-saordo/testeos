<properties
   pageTitle="Tutorial: Procesamiento de facturas EDIFACT mediante Servicios de BizTalk de Azure | Servicios de BizTalk de Microsoft Azure"
   description="Creación y configuración del conector de Box o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/29/2016"
   ms.author="deonhe"/>

# Tutorial: Procesamiento de facturas EDIFACT mediante los Servicios de BizTalk de Azure
Puede usar el Portal de Servicios de BizTalk para configurar e implementar los contratos X12 y EDIFACT. En este tutorial, vemos cómo crear un contrato EDIFACT para intercambiar facturas entre socios comerciales. Este tutorial se centra en una solución empresarial integral que involucra a dos socios comerciales, Northwind y Contoso, que intercambian mensajes EDIFACT.

## Ejemplo basado en este tutorial
Este tutorial se ha escrito en torno a un ejemplo **Envío de facturas EDIFACT a través de los servicios de BizTalk**, que está disponible en la [Galería de código de MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Puede usar el ejemplo y seguir este tutorial para entender cómo se creó. O bien, puede usar este tutorial para crear su propia solución desde cero. Este tutorial está destinado al segundo enfoque, de modo que entienda cómo se compiló esta solución. Además, en la medida de lo posible, el tutorial es coherente con el ejemplo y usa los mismos nombres para los artefactos (por ejemplo, esquemas, transformaciones) que se usan en el ejemplo.

>[AZURE.NOTE] Dado que esta solución implica enviar un mensaje de un puente EAI a un puente EDI, se reutiliza el ejemplo de [BizTalk Services Bridge chaining sample](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104).

## ¿Cómo funciona la solución?

En esta solución, Northwind recibe facturas EDIFACT de Contoso. Estas facturas no están en un formato EDI estándar. Por eso, antes de enviar la factura a Northwind, se debe transformar a una factura EDIFACT (también llamada INVOIC). Al recibirla, Northwind debe procesar la factura EDIFACT y devolver un mensaje de control (también llamado CONTRL) a Contoso.

![][1]

Para lograr este escenario empresarial, Contoso usa las funciones que se incluyen en Servicios de BizTalk de Microsoft Azure.

*   Contoso usa puentes EAI para transformar la factura original en INVOIC EDIFACT.

*   El puente EAI envía luego el mensaje a un puente de envío de EDI implementado como parte de un contrato configurado en el Portal de Servicios de BizTalk.

*   El puente de envío EDI procesa la INVOIC EDIFACT y la redirige a Northwind.

*   Después de recibir la factura, Northwind devuelve un mensaje CONTRL al puente de recepción EDI implementado como parte del contrato.

> [AZURE.NOTE] Como alternativa, esta solución también demuestra cómo usar lotes para enviar facturas en lotes, en lugar de enviar cada factura por separado.

Para completar el escenario, usamos colas de Bus de servicio para enviar la factura de Contoso a Northwind o para recibir la confirmación de Northwind. Estas colas se pueden crear con una aplicación cliente, que está disponible para descarga y se incluye en el paquete de ejemplo disponible como parte de este tutorial.

## Requisitos previos

*   Debe tener un espacio de nombres de Bus de servicio. Para instrucciones sobre cómo crear un espacio de nombres, consulte [How To: Create or Modify a Service Bus Service Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supongamos que ya tiene un espacio de nombres del Bus de servicio aprovisionado, llamado **edifactbts**.

*   Debe tener una suscripción a Servicios de BizTalk. Para instrucciones, consulte [Creación de Servicios de BizTalk mediante el Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=302280). En este tutorial, supongamos que tiene una suscripción a Servicios de BizTalk, llamada **contosowabs**.

*   Registre la suscripción de Servicios de BizTalk en el Portal de Servicios de BizTalk. Para obtener instrucciones, consulte [Registro de una implementación de Servicios de BizTalk en el Portal de Servicios de BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Debe tener instalado Visual Studio.

*   Debe tener instalado el SDK de Servicios de BizTalk. Puede descargar el SDK de [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)

## Paso 1: Creación de las colas de Bus de servicio  
Esta solución usa colas de Bus de servicio para intercambiar mensajes entre socios comerciales. Contoso y Northwind envían mensajes a las colas desde donde los puentes EAI o EDI los consumen. Para esta solución, necesita tres colas de Bus de servicio:

*   **northwindreceive**: Northwind recibe la factura de Contoso por esta cola.

*   **contosoreceive**: Contoso recibe la confirmación de Northwind por esta cola.

*   **suspended** : todos los mensajes suspendidos se enrutan a esta cola. Los mensajes se suspenden si tienen un error durante el procesamiento.

Puede crear estas colas de Bus de servicio mediante una aplicación cliente que se incluye en el paquete de ejemplo.

1.  Desde la ubicación donde descargó el ejemplo, abra **Tutorial Sending Invoices Using BizTalk Services EDI Bridges.sln**.

2.  Presione **F5** para compilar e iniciar la aplicación **Tutorial Client**.

3.  En la pantalla, escriba el espacio de nombres ACS del Bus de servicio, el nombre del emisor y la clave del emisor.

    ![][2]  
4.  Aparece un mensaje que indica que se crearán las tres colas en el espacio de nombres del Bus de servicio. Haga clic en **Aceptar**.

5.  Deje Tutorial Client en ejecución. Haga clic en **Bus de servicio** > **_el espacio de nombres del Bus de servicio_** > **Colas**, y compruebe que se hayan creado las tres colas.

## Paso 2: Creación e implementación del contrato entre socios comerciales
Cree un contrato entre los socios comerciales Contoso y Northwind. Un contrato entre socios comerciales define un contrato mercantil entre los dos socios empresariales, por ejemplo, qué esquema de mensajes se usará, qué protocolo de mensajería, etc. Un contrato entre socios comerciales incluye dos puentes EDI, uno para enviar mensajes a los socios comerciales (denominado el **puente de envío EDI**) y otro para recibir mensajes de los socios comerciales (denominado el **puente de recepción EDI**).

En el contexto de esta solución, el puente de envío EDI corresponde al lado de envío del contrato y se usa para enviar la factura EDIFACT de Contoso a Northwind. De igual modo, el puente de recepción EDI corresponde al lado de recepción del contrato y se usa para recibir las confirmaciones de Northwind.

### Creación de los socios comerciales

Para comenzar, cree los socios comerciales de Contoso y Northwind.

1.  En el Portal de Servicios de BizTalk, en la pestaña **Socios**, haga clic en **Agregar**.

2.  En la página Nuevo socio comercial, escriba **Contoso** como nombre del socio y, a continuación, haga clic en **Guardar**.

3.  Repita el mismo paso para crear el segundo socio, **Northwind**.

### Creación del contrato
Los contratos entre socios comerciales se crean entre perfiles de negocio de socios comerciales. Esta solución usa los perfiles de socio predeterminados que se crean automáticamente cuando se crean los socios.

1.  En el Portal de Servicios de BizTalk, haga clic en **Contratos** > **Agregar**.

2.  En la página **Configuración general** del nuevo contrato, especifique los valores como se muestra en la imagen siguiente y, luego, haga clic en **Continuar**.

    ![][3]

    Después de hacer clic en **Continuar**, dos pestañas, **Configuración de recepción** y **Configuración de envío** se vuelven disponibles.

3.  Cree el contrato de envío entre Contoso y Northwind. Este contrato rige la forma en que Contoso envía la factura EDIFACT a Northwind.

    1.  Haga clic en **Configuración de envío**.

    2.  Conserve los valores predeterminados de las pestañas **Dirección URL de entrada**, **Transformación** y **Procesamiento por lotes**.

    3.  En la pestaña **Protocolo**, en la sección **Esquemas**, cargue el esquema **EFACT\_D93A\_INVOIC.xsd**. Este esquema está disponible con el paquete de ejemplo.

        ![][4]  
    4.  En la pestaña **Transformación**, especifique los detalles de las colas de Bus de servicio. Para el contrato del lado de envío, usamos la cola **northwindreceive** para enviar la factura EDIFACT a Northwind y la cola **suspended** para enrutar los mensajes que se suspenden por tener errores durante el procesamiento. Estas colas se crean en **Paso 1: Creación de las colas de Bus de servicio** (en este tema).

        ![][5]

        En **Configuración de transporte > Tipo de transporte** y **Configuración de suspensión de mensaje > Tipo de transporte**, seleccione Bus de servicio de Azure y proporcione los valores como se muestra en la imagen.

4.  Cree el contrato de recepción entre Contoso y Northwind. Este contrato rige la forma en que Contoso recibe la confirmación de Northwind.

    1.  Haga clic en **Configuración de recepción**.

    2.  Conserve los valores predeterminados de las pestañas **Transporte** y **Transformación**.

    3.  En la pestaña **Protocolo**, en la sección **Esquema**, cargue el esquema **EFACT\_4.1\_CONTRL.xsd**. Este esquema está disponible con el paquete de ejemplo.

    4.  En la pestaña **Ruta**, cree un filtro para asegurarse de que solo las confirmaciones de Northwind se enruten a Contoso. En **Configuración de ruta**, haga clic en **Agregar** para crear el filtro de enrutamiento.

        ![][6]
        1.  Proporcione valores para **Nombre de regla**, **Regla de ruta** y **Destino de ruta**, como se muestra en la imagen.

        2.  Haga clic en **Guardar**.

    5.  De nuevo en la pestaña **Ruta**, especifique adónde se enrutan las confirmaciones suspendidas (confirmaciones con error durante el procesamiento). Configure el tipo de transporte como Bus de servicio de Azure, el tipo de destino de ruta como **Cola**, el tipo de autenticación como **Firma de acceso compartido** (SAS), proporcione la cadena de conexión de SAS para el espacio de nombres del Bus de servicio y, a continuación, escriba el nombre de la cola como **suspended**.

5.  Por último, haga clic en **Implementar** para implementar el contrato. Anote los puntos de conexión donde se implementan los contratos de envío y recepción.

    *   En la pestaña **Configuración de envío**, en **Dirección URL de entrada**, anote el punto de conexión. Para enviar un mensaje de Contoso a Northwind con el puente de envío EDI, debe enviar un mensaje a este punto de conexión.

    *   En la pestaña **Configuración de recepción**, en **Transporte**, anote el punto de conexión. Para enviar un mensaje de Northwind a Contoso mediante el puente de envío EDI, debe enviar un mensaje a este punto de conexión.

## Paso 3: Creación e implementación del proyecto de Servicios de BizTalk

En el paso anterior, implementó los contratos de envío y recepción de EDI para procesar facturas y confirmaciones EDIFACT. Estos contratos solo pueden procesar mensajes que cumplan el esquema de mensaje EDIFACT estándar. Sin embargo, según el escenario de esta solución, Contoso envía una factura a Northwind en un esquema interno propio. Por lo tanto, antes de enviar el mensaje al puente de envío EDI, se debe transformar del esquema interno al esquema de facturas EDIFACT estándar. El proyecto de EAI de Servicios de BizTalk hace eso.

El proyecto de Servicios de BizTalk, **InvoiceProcessingBridge**, que transforma el mensaje también se incluye como parte del ejemplo que descargó. El proyecto incluye los siguientes artefactos:

*   **INHOUSEINVOICE. XSD**: esquema de la factura interna que se envía a Northwind.

*   **EFACT\_D93A\_INVOIC. XSD**: esquema de la factura EDIFACT estándar.

*   **EFACT\_4.1\_CONTRL. XSD**: esquema de la confirmación de EDIFACT que Northwind envía a Contoso.

*   **INHOUSEINVOICE\_to\_D93AINVOIC. TRFM**: la transformación que asigna el esquema de facturas interno al esquema de facturas EDIFACT estándar.

### Creación del proyecto de Servicios de BizTalk
1.  En la solución de Visual Studio, expanda el proyecto InvoiceProcessingBridge y luego abra el archivo **MessageFlowItinerary.bcs**.

2.  Haga clic en cualquier parte del lienzo y establezca la **dirección URL del servicio de BizTalk** en el cuadro de propiedad para especificar el nombre de la suscripción de Servicios de BizTalk. Por ejemplo: `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  En el cuadro de herramientas, arrastre un **puente unidireccional Xml** al lienzo. Establezca las propiedades del puente **Nombre de entidad** y **Dirección relativa** en **ProcessInvoiceBridge**. Haga doble clic en **ProcessInvoiceBridge** para abrir la superficie de configuración del puente.

4.  En el cuadro **Tipos de mensaje**, haga clic en el botón del signo de la suma (**+**) para especificar el esquema del mensaje entrante. Como el mensaje entrante para el puente EAI es siempre la factura interna, configúrelo en **INHOUSEINVOICE**.

    ![][8]  
5.  Haga clic en la forma **Transformación XML** y, en el cuadro de propiedad, en la propiedad **Asignaciones**, haga clic en el botón de puntos suspensivos (**...**). En el cuadro de diálogo **Selección de asignaciones**, seleccione el archivo de transformación **INHOUSEINVOICE\_to\_D93AINVOIC** y luego haga clic en **Aceptar**.

    ![][9]  
6.  Vuelva a **MessageFlowItinerary.bcs** y, en el cuadro de herramientas, arrastre un **punto de conexión de servicio externo bidireccional** a la derecha de **ProcessInvoiceBridge**. Establezca su propiedad **Nombre de entidad** en **EDIBridge**.

7.  En el Explorador de soluciones, expanda **MessageFlowItinerary.bcs** y haga doble clic en el archivo **EDIBridge.config**. Reemplace el contenido de **EDIBridge.config** por lo siguiente.

    > [AZURE.NOTE] ¿Por qué es necesario editar el archivo .config? El punto de conexión de servicio externo que agregamos al lienzo del diseñador de puentes representa los puentes EDI que implementamos antes. Los puentes EDI son puentes bidireccionales, con un lado de envío y otro de recepción. Sin embargo, el puente EAI que agregamos al diseñador de puentes es unidireccional. Por lo tanto, para controlar los distintos patrones de intercambio de mensajes de los dos puentes, usamos un comportamiento de puente personalizado e incluimos su configuración en el archivo .config. Además, el comportamiento personalizado también controla la autenticación en el punto de conexión del puente de envío EDI. Este comportamiento personalizado está disponible como un ejemplo independiente en [BizTalk Services Bridge chaining sample - EAI to EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Esta solución reutiliza el ejemplo.
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Actualice el archivo EDIBridge.config para incluir detalles de configuración.

    *   En _<behaviors>_, proporcione el espacio de nombres ACS y la clave asociada a la suscripción de Servicios de BizTalk.

    *   En _<client>_, proporcione el punto de conexión donde se implementa el contrato de envío EDI.

    Guarde los cambios y cierre el archivo de configuración.

9.  En el cuadro de herramientas, haga clic en el **Conector** y únase a los componentes **ProcessInvoiceBridge** y **EDIBridge**. Seleccione el conector y, en el cuadro Propiedades, establezca **Condición de filtro** en **Coincidir todo**. Esto garantiza que todos los mensajes procesados por el puente EAI se enrutan al puente EDI.

    ![][10]  
10.  Guarde los cambios en la solución.  

### Implementación del proyecto

1.  En el equipo donde creó el proyecto de Servicios de BizTalk, descargue e instale el certificado SSL para su suscripción de Servicios de BizTalk. En Servicios de BizTalk, haga clic en **Panel** y luego haga clic en **Descargar certificado de SSL**. Haga doble clic en el certificado y siga las indicaciones para completar la instalación. Asegúrese de instalar el certificado en el almacén de certificados **Entidades de certificación raíz de confianza**.

2.  En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto **InvoiceProcessingBridge** y luego haga clic en **Implementar**.

3.  Proporcione los valores como se muestra en la imagen y luego haga clic en **Implementar**. Para obtener las credenciales de ACS para Servicios de BizTalk, haga clic en **Información de conexión** en el panel de Servicios de BizTalk.

    ![][11]

    En el panel de resultados, copie el punto de conexión donde se implementa el puente EAI, por ejemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Necesitará esta dirección URL del punto de conexión más adelante.

## Paso 4: Prueba de la solución


En este tema, examinaremos cómo probar la solución mediante la aplicación **Tutorial Client** proporcionada como parte del ejemplo.

1.  En Visual Studio, presione F5 para iniciar **Tutorial Client**.

2.  La pantalla debe mostrar los valores completados en el paso donde creamos las colas de Bus de servicio. Haga clic en **Siguiente**.

3.  En la siguiente ventana, proporcione las credenciales de ACS para la suscripción de Servicios de BizTalk y los puntos de conexión donde se implementan los puentes EAI y EDI (recepción).

    Ya copió el punto de conexión del puente EAI en el paso anterior. Para el punto de conexión del puente de recepción EDI, en el Portal de Servicios de BizTalk, vaya al contrato > Configuración de recepción > Transporte > Punto de conexión.

    ![][12]  
4.  En la ventana siguiente, en Contoso, haga clic en el botón **Enviar factura interna**. En el cuadro de diálogo Abrir, abra el archivo INHOUSEINVOICE.txt. Examine el contenido del archivo y luego haga clic en **Aceptar** para enviar la factura.

    ![][13]  
5.  En unos segundos, Northwind recibe la factura. Haga clic en el vínculo **Ver mensaje** para ver la factura recibida por Northwind. Observe cómo la factura que recibe Northwind está en formato EDIFACT estándar, mientras que la enviada por Contoso estaba en formato interno.

    ![][14]  
6.  Seleccione la factura y luego haga clic en **Enviar confirmación**. En el cuadro de diálogo que aparece, observe que el identificador de intercambio es igual en la factura recibida y en la confirmación enviada. Haga clic en Aceptar en el cuadro de diálogo **Enviar confirmación**.

    ![][15]  
7.  En unos segundos, Contoso recibe la confirmación correctamente.

    ![][16]

## Paso 5 (opcional): Envío de facturas EDIFACT en lotes 
Los puentes EDI de Servicios de BizTalk también admiten el procesamiento en lotes de los mensajes salientes. Esta función es útil para los socios receptores que prefieren recibir un lote de mensajes (que cumpla ciertos criterios), en lugar de mensajes individuales.

El factor más importante al trabajar con lotes es la emisión real del lote, también llamado el criterio de liberación. Este criterio de liberación se puede basar en cómo quiere recibir los mensajes el socio receptor. Si el procesamiento por lotes está habilitado, el puente EDI no envía el mensaje saliente al socio receptor hasta que se cumple el criterio de liberación. Por ejemplo, un criterio de procesamiento en lotes basado en el tamaño del mensaje libera un lote una vez que se procesan "n" mensajes en el lote. Un criterio de procesamiento por lotes también se puede basar en la hora, de modo que se envíe un lote a una hora fijada todos los días. En esta solución, analizamos los criterios basados en el tamaño del mensaje.

1.  En el Portal de Servicios de BizTalk, haga clic en el contrato que creó antes. Haga clic en Configuración de envío > Procesamiento por lotes > Agregar lote.

2.  Para el nombre del lote, escriba **InvoiceBatch**, proporcione una descripción y luego haga clic en **Siguiente**.

3.  Especifique un criterio de procesamiento por lotes, que defina qué mensajes se deben procesar por lotes. En esta solución, procesamos por lote todos los mensajes. Así que, seleccione la opción Usar definiciones avanzadas y escriba **1 = 1**. Esta es una condición que siempre será verdadera y, por eso, todos los mensajes se procesarán por lotes. Haga clic en **Siguiente**.

    ![][17]  
4.  Especifique un criterio de liberación por lotes. En la lista desplegable, seleccione **MessageCountBased** y, para **Recuento**, especifique **3**. Es decir, se enviará a Northwind un lote de tres mensajes. Haga clic en **Siguiente**.

    ![][18]  
5.  Revise el resumen y luego haga clic en **Guardar**. Haga clic en **Implementar** para volver a implementar el contrato.

6.  Vuelva a **Tutorial Client**, haga clic en **Enviar factura interna** y siga las indicaciones para enviar la factura. Verá que Northwind no recibe ninguna factura porque no se cumple el tamaño del lote. Repita este paso dos veces para tener tres mensajes de factura para enviar a Northwind. Con esto, se cumple el criterio de liberación del lote de tres mensajes, por lo que ahora se debería ver una factura en Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

<!---HONumber=AcomDC_0302_2016-->
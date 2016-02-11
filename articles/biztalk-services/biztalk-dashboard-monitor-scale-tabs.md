<properties 
	pageTitle="Panel, Monitor, Escala, Configurar y Conexiones híbridas en Servicios de BizTalk | Microsoft Azure" 
	description="Obtenga información acerca de los controles y la supervisión del rendimiento en las pestañas del Portal de Azure clásico para Servicios de BizTalk: Panel, Monitor, Escala, Configurar y Conexiones híbridas. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mandia"/>




# Revise las pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas

Después de crear su servicio de BizTalk e implementar su aplicación, puede cambiar algunas de las configuraciones del servicio de BizTalk y supervisar el rendimiento de la aplicación.

Al abrir el Portal de Azure clásico, entrará automáticamente en la pestaña **TODOS LOS ELEMENTOS**. Para ver su servicio de BizTalk, selecciónelo en la pestaña **TODOS LOS ELEMENTOS** o seleccione la pestaña **SERVICIOS DE BIZTALK** y, a continuación, haga clic en el nombre de su servicio de BizTalk.

De este modo se abre una nueva ventana con las pestañas siguientes. El tema describe estas pestañas.

## Inicio rápido (![Inicio rápido][QuickStart])
En función de la edición de Servicios de BizTalk, puede que no estén disponibles todas las opciones mostradas.
<table border="1">
    <tr>
        <td><strong>Get the tools</strong></td>

        <td>Download the BizTalk Services SDK to install the Visual Studio project templates on your on-premises development computer. These templates create the <strong>BizTalk Services</strong> (bridge) and the <strong>BizTalk Service Artifacts</strong> (Transform) Visual Studio projects that are deployed to your BizTalk Service.
        <br/><br/>
		<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK </a> and <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK</a> lists the steps to get started.
        </td>
    </tr>

    <tr>
        <td><strong>Create partner agreements</strong></td>

        <td>Opens the Azure BizTalk Services Portal hosted on Azure where you add partners and create X12, AS2, and EDIFACT EDI agreements.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal</a> lists the steps to get started.
        </td>
    </tr>

<tr>
        <td><strong>Learn more about BizTalk Services</strong></td>
        <td>Go to the <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">learning center</a> to learn more about Azure BizTalk Services.</td>
</tr>
</table>


En la barra de tareas de la parte inferior, puede:

<table border="1">

<tr>
<td><strong>Administrar</strong> su implementación de la aplicación</td>
<td>Abre el portal de Servicios de BizTalk de Azure. El Portal de Servicios de BizTalk es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.
<br/><br/>
Esto es lo mismo que <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes para la mensajería EDI en el portal de los servicios de BizTalk</a> proporciona más información acerca del Portal de Servicios de BizTalk.</td>
</tr>

<tr>
<td><strong>Información de conexión</strong> del espacio de nombres de control de acceso</td>
<td>Al seleccionar Información de conexión, aparecen el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave predeterminada. Estos valores se pueden copiar.
<br/><br/>
Además, puede abrir el Portal de control de acceso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Creación de un espacio de nombres de control de acceso</a> proporciona más información acerca del Portal de control de acceso.</td>
</tr>

<tr>
<td><strong>Sincronizar claves</strong> de la cuenta de almacenamiento</td>
<td>Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.
<br/><br/>
Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:
<br/><br/>
<ol>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>En el Portal de Azure clásico, seleccione la cuenta de almacenamiento y Regenerar la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>En el Portal de Azure clásico, seleccione la cuenta de almacenamiento y Regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se llama "claves de sustitución". Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> su aplicación</td>
<td>Al seleccionar en Eliminar, se elimina su servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</table>


## Panel
En función de la edición de Servicios de BizTalk, puede que no estén disponibles todas las opciones mostradas.

Al seleccionar el nombre de su servicio de BizTalk, aparece la pestaña Panel. En el Panel, existen las siguientes opciones:

##### Información general del uso: muestra el número de conexiones híbridas usadas
También muestra el uso de datos en GB.

##### Gráfico de métrica: muestra una lista fija de métricas de rendimiento
Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. También puede elegir los valores **Relativo** o **Absoluto** y el intervalo de tiempo **Intervalo** de las métricas que se muestran en el gráfico.

Para ver una descripción de estas métricas de rendimiento, vaya a la sección [Métricas disponibles](#Metrics) de este tema.


##### Vista rápida: muestra las propiedades del servicio de BizTalk

<table border="1">

<tr>
<td><strong>Actualizar credenciales de la base de datos de seguimiento</strong></td>
<td>Cambia el nombre de usuario y la contraseña usados para iniciar sesión en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Actualizar certificado SSL</strong></td>
<td>Puede actualizar el servicio de BizTalk para que use un certificado SSL diferente. Al <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">crear el servicio de BizTalk</a>, se crea automáticamente un certificado SSL autofirmado.</td>
</tr>
<tr>
<td><strong>Descargar certificado</strong></td>
<td>Puede descargar el certificado SSL usado por su Servicio de BizTalk en una máquina local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Muestra el estado actual del Servicio de BizTalk. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Gráfico del estado de servicio de BizTalk</a> </td>
</tr>
<tr>
<td><strong>URL de servicio</strong></td>
<td>Dirección URL para el Servicio de BizTalk. Esto es lo mismo que la <strong>URL de dominio</strong> especificada al crear el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Dirección IP virtual (VIP) pública</strong></td>
<td>La dirección IP asignada al Servicio de BizTalk. Se usa para todos los extremos de entrada y es la dirección de origen para el tráfico saliente. Esta dirección IP pertenece al servicio de BizTalk mientras esté creado. Si elimina el servicio de BizTalk, la dirección IP se asigna a otro Servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>Espacio de nombres ACS</strong></td>
<td>Se autentica con el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Edición</strong></td>
<td>Muestra la edición especificada al crear el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Ubicación</strong></td>
<td>Muestra la región geográfica donde se hospeda el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Creado</strong></td>
<td>Muestra la fecha y la hora en que se creó el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Base de datos de seguimiento</strong></td>
<td>El nombre de la base de datos SQL de Azure que almacena las tablas de seguimiento usadas por su servicio de BizTalk. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicados</a> proporciona detalles en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Almacenamiento de supervisión y archivado</strong></td>
<td>La cuenta de almacenamiento de Azure que almacena el resultado de supervisión del servicio de BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicados</a> proporciona detalles en la cuenta de almacenamiento.</td>
</tr>
<tr>
<td><strong>Nombre de suscripción</strong></td>
<td>Muestra la suscripción que hospeda el Servicio de BizTalk. La suscripción rige el acceso al Portal de Azure clásico.</td>
</tr>
<tr>
<td><strong>Id. de suscripción</strong></td>
<td>Cuando se crea una suscripción, se genera automáticamente un identificador de suscripción. Cuando se usan las API REST, puede que tenga que especificar el identificador de suscripción.</td>
</tr>
</table>

[Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280) incluye los pasos para crear un servicio de BizTalk.


##### Administrar, Información de conexión, Sincronizar claves y Eliminar en la barra de tareas:

<table border="1">

<tr>
<td><strong>Administrar</strong> su implementación de la aplicación</td>
<td>Abre el portal de Servicios de BizTalk de Azure. El Portal de Servicios de BizTalk es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.
<br/><br/>
Esto es lo mismo que <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes para la mensajería EDI en el portal de los servicios de BizTalk</a> proporciona más información acerca del Portal de Servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>Información de conexión</strong> del espacio de nombres de control de acceso</td>
<td>Muestra los valores del espacio de nombres de control de acceso, el emisor predeterminado y la clave predeterminada, que se pueden copiar.
<br/><br/>
Además, puede abrir el Portal de control de acceso. Este portal de control de acceso es lo mismo que usar la opción de Active Directory en el panel de navegación izquierdo.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administración del espacio de nombres del ACS</a> proporciona más información sobre el Portal de control de acceso.</td>
</tr>
<tr>
<td><strong>Sincronizar claves</strong> de la cuenta de almacenamiento</td>
<td>Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.
<br/><br/>
Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:
<br/><br/>
<ol>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>En el Portal de Azure clásico, seleccione la cuenta de almacenamiento y Regenerar la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>En el Portal de Azure clásico, seleccione la cuenta de almacenamiento y Regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se llama "claves de sustitución". Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> su aplicación</td>
<td>Se elimina su Servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</table>


## Supervisión
No se aplica a la versión gratuita.

Al seleccionar el nombre de su Servicio de BizTalk, la pestaña Supervisar se vuelve disponible y muestra lo siguiente:

##### Gráfico de métrica: muestra las métricas de rendimiento seleccionadas.
Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. Se pueden elegir las métricas de rendimiento que mostrar. Se pueden mostrar seis métricas de rendimiento a la vez como máximo.

También puede elegir los valores **Relativo** o **Absoluto** y el intervalo de tiempo **Intervalo** de las métricas que se muestran.

##### Para eliminar o mostrar métricas en el gráfico:
1. Seleccione la pestaña **Monitor**.
2. Haga clic en **Agregar métricas** en la barra de tareas: ![Seleccione Agregar métricas][AddMetrics]
3. Compruebe las métricas de rendimiento que quiera mostrar.
4. Seleccione la marca de verificación para volver a la pestaña **Monitor**.
5. Seleccione el círculo situado junto a la métrica para mostrar el valor de dicha métrica en el gráfico.  

	Por ejemplo, la métrica de **Uso de CPU** está atenuada; su salida no se muestra en el gráfico: ![La métrica Uso de CPU está atenuada][GrayedMetric]

	Seleccione el círculo atenuado para habilitar la métrica de **Uso de CPU** para mostrar su resultado en el gráfico: ![La métrica Uso de CPU está habilitada][EnabledMetric]

6. Para eliminar una métrica del gráfico mostrado y de la lista, seleccione **Eliminar métrica** en la barra de tareas. Para agregar la métrica a lista otra vez, seleccione **Agregar métricas** en la barra de tareas, compruebe la métrica y seleccione la marca de verificación para volver a la pestaña **Monitor**. Seleccione el círculo gris para habilitar la métrica.

## <a name="Metrics"></a>Métricas disponibles
Están disponibles las siguientes métricas y contadores de rendimiento:

<table border="1">

<tr>
<td><strong>Latencia de recorrido de ida y vuelta</strong></td>
<td>Muestra el tiempo medio en milisegundos (ms) que se tarda en procesar un mensaje desde el momento en que se recibe hasta que el Servicio de BizTalk lo procesa por completo en todos los puentes. Solo se cuentan los mensajes correctamente procesados.<br/><br/>
Cuando se producen los eventos siguientes se crea una marca de tiempo:
<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br/>
Esta métrica muestra el resultado del cálculo siguiente:
<br/><br/>
[Respuesta de confirmación de destino enviada a la puerta de enlace] - [El mensaje entra por la puerta de enlace]</td>
</tr>
<tr>
<td><strong>Errores en origen</strong></td>
<td>Muestra el número total de mensajes que el Servicio de BizTalk no pudo extraer de los extremos de origen.</td>
</tr>
<tr>
<td><strong>Uso de CPU</strong></td>
<td>Incluye el porcentaje de tiempo medio del procesador de todas las instancias de rol.</td>
</tr>
<tr>
<td><strong>Latencia de procesamiento</strong></td>
<td>Muestra el tiempo medio en milisegundos (ms) que tarda el Servicio de BizTalk en procesar un mensaje en todos los puentes, excluyendo el tiempo empleado en los destinos. Solo se cuentan los mensajes correctamente procesados.<br/><br/>
Cuando se produce cada uno de los eventos siguientes se crea una marca de tiempo:

<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br/>Esta métrica muestra el resultado del cálculo siguiente:<br/><br/>
[Respuesta de confirmación de destino enviada a la puerta de enlace] - [Mensaje que entra por la puerta de enlace] - [Se recibe la respuesta del destino] + [El mensaje se envía al destino]</td>
</tr>
<tr>
<td><strong>Errores en proceso</strong></td>
<td>Muestra el número total de mensajes con error durante el procesamiento del Servicio BizTalk en todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes enviados</strong></td>
<td>Muestra el número total de mensajes enviados por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje enviado desde un proceso alcanza el destino de la ruta. Esta métrica no indica el correcto procesamiento del mensaje.<br/><br/>
En un escenario de respuesta de solicitud, la métrica se incrementa cuando el destino de la ruta envía una confirmación de recibo al proceso.</td>
</tr>
<tr>
<td><strong>Mensajes recibidos</strong></td>
<td>Muestra el número total de mensajes recibidos por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe un nuevo mensaje.</td>
</tr>
<tr>
<td><strong>Mensajes en proceso</strong></td>
<td>Muestra el número total de mensajes que el Servicio de BizTalk está procesando actualmente dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes procesados</strong></td>
<td>Muestra el número total de mensajes procesados correctamente por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe correctamente un mensaje y lo envía al destino.</td>
</tr>
</table>


## Escala
En la pestaña Escala puede sumar o restar el número de unidades usadas por su servicio de BizTalk. De forma predeterminada, hay una unidad configurada. Se pueden agregar unidades adicionales para escalar su servicio de BizTalk. Cuando se incrementa la escala, se aumenta el rendimiento. La cantidad de recursos también aumenta, incluyendo los puentes implementados, los acuerdos, las conexiones de LOB y la eficacia de procesamiento. Por ejemplo, si aumenta la escala de 1 unidad a 2. En esta situación, puede implementar el doble de puentes, acuerdos, conexiones de LOB y eficacia de procesamiento.

Algunas ediciones de BizTalk no ofrecen la opción de escala. En esta situación, se admite una unidad. A fin de determinar cuántas unidades puede escalar su edición, consulte [Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md).

El aumento del número de unidades podría afectar al precio. Si aumenta las unidades, al seleccionar **Guardar** aparece un mensaje que le indica si la facturación se ve afectada. A continuación, seleccione Continue. Cuando aumente el número de unidades, el estado del servicio de BizTalk cambia de Activo a Actualizando. En el estado Actualizando, su servicio de BizTalk continúa ejecutándose.

[Servicios de BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md) define una unidad.


## Configuración
No se aplica a conexiones híbridas.

Establezca Estado de copia de seguridad en Ninguno o Automático. Cuando se establezca en Ninguno, no se creará ninguna copia de seguridad automáticamente. Cuando se establece en Automático, configure la ubicación de la copia de seguridad, la frecuencia de la misma y cuánto tiempo quiere conservar los archivos de copia de seguridad.

[Servicios de BizTalk: copias de seguridad y restauración](biztalk-backup-restore.md) proporciona los detalles.


## <a name="HybridConnections"></a>Conexiones híbridas
Las conexiones híbridas conectan una aplicación de Azure, como sitios web o Servicios móviles, a un recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API web HTTP, Servicio móviles y la mayoría de los servicios web personalizados. Las conexiones híbridas se administran en los Servicios de BizTalk en el Portal de Azure clásico.

Para crear conexiones híbridas en Sitios web de Azure, consulte [Conexión híbrida: conexión de un sitio web de Azure a un recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Para usar conexiones híbridas en Servicios móviles de Azure, consulte [Servicios móviles de Azure y conexiones híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).

Para crear o administrar conexiones híbridas en Servicios de BizTalk de Azure, consulte [Conexiones híbridas](integration-hybrid-connection-overview.md).



## Pasos siguientes
Ahora que ya se ha familiarizado con las diferentes pestañas, puede obtener más información acerca de las características de los servicios de BizTalk de Azure:

- [Servicios de BizTalk: limitaciones](biztalk-throttling-thresholds.md)  
- [Servicios de BizTalk: nombre del emisor y clave del emisor](biztalk-issuer-name-issuer-key.md)  
- [Servicios de BizTalk: copias de seguridad y restauración](biztalk-backup-restore.md)

## Otras referencias
- [Conexiones híbridas](integration-hybrid-connection-overview.md)  
- [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](biztalk-editions-feature-chart.md)  
- [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](biztalk-provision-services.md)  
- [Servicios de BizTalk: gráfico de estado del servicio de BizTalk](biztalk-service-state-chart.md)  
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 

<!---HONumber=AcomDC_1203_2015-->
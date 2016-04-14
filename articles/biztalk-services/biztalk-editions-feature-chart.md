<properties
	pageTitle="Información acerca de las características de las ediciones de Servicios de BizTalk | Microsoft Azure"
	description="Compare las capacidades de las ediciones de Servicios de BizTalk: Free, Developer, Basic, Standard y Premium. MABS, WABS."
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/29/2016"
	ms.author="mandia"/>


# Servicios de BizTalk: gráfico de ediciones

Servicios de BizTalk de Azure ofrece varias ediciones. Use este artículo para determinar qué edición es la adecuada para sus escenario y necesidades empresariales.


## Comparar las ediciones

**Free (Vista previa)**

Permite crear y administrar conexiones híbridas. Una conexión híbrida es una manera sencilla de conectar un sitio web de Azure a un recurso local, como SQL Server.

**Developer**

Incluye conexiones híbridas y procesamiento de mensajes de EAI y EDI con un portal de administración fácil de utilizar para socios comerciales, además de compatibilidad con esquemas EDI y procesamiento enriquecido EDI sobre X12 y AS2. Puede crear escenarios comunes de EAI que conectan servicios en la nube con cualquier protocolo HTTP/S, REST, FTP, WCF y SFTP para leer y escribir mensajes. Utilice la conectividad en sistemas locales de LOB con adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel y SQL Server listos para utilizar. Utilice un entorno centrado en el desarrollador con herramientas de Visual Studio para desarrollo e implementación simples. Limitado a fines de desarrollo y prueba solo sin Contrato de nivel de servicio (SLA).

**Básica**

Incluye la mayoría de las capacidades de la edición Developer con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También ofrece alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).

**Standard**

Incluye la mayoría de las capacidades de la edición Basic con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También ofrece alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).

**Premium**

Incluye la mayoría de las capacidades de la edición Standard con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También incluye el archivado, la alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).


## Gráfico de ediciones
En la tabla siguiente se muestran las diferencias.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Vista previa)</th>
        <th>Developer</th>
        <th>Básica</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Precio de salida</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de precios de Azure</a></td>
</tr>
<tr>
<td><strong>Configuración mínima predeterminada</strong></td>
<td>1 unidad gratis</td>
<td>1 unidad Developer</td>
<td>1 unidad Basic</td>
<td>1 unidad Standard</td>
<td>1 unidad Premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Sí, en incrementos de 1 unidad Basic</td>
<td>Sí, en incrementos de una unidad Standard</td>
<td>Sí, en incrementos de una unidad Premium</td>
</tr>
<tr>
<td><strong>Escalamiento horizontal máximo permitido</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Hasta ocho unidades</td>
<td>Hasta ocho unidades</td>
<td>Hasta ocho unidades</td>
</tr>
<tr>
<td><strong>Puentes EAI por unidad</strong></td>
<td>No se incluye</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Incluye contratos TPM</td>
<td>No se incluye</td>
<td>Se incluye. 10 contratos por unidad.</td>
<td>Se incluye. 50 contratos por unidad.</td>
<td>Se incluye. 250 contratos por unidad.</td>
<td>Se incluye. 1000 contratos por unidad.</td>
</tr>
<tr>
<td><strong>Conexiones híbridas por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferencia de datos de conexiones híbridas (GB) por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexiones del servicio de adaptador BizTalk a sistemas locales de LOB</strong></td>
<td>No se incluye</td>
<td>1 conexión</td>
<td>2 conexiones</td>
<td>5 conexiones</td>
<td>25 conexiones</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos compatibles:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus de servicio</li>
<li>Blob de Azure</li>
<li>API de REST</li>
</ul>
</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Alta disponibilidad</strong>
<br/><br/>
Para ver el Contrato de nivel de servicio (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.
</td>
<td>No se incluye</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Copia de seguridad y restauración</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Seguimiento</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Archivado</strong><br/><br/>
Incluye la recepción sin rechazo (NRR) y la descarga de mensajes controlados</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>No se incluye</td>
<td>No se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Uso de transformaciones, incluido XSLT personalizado</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
</table>

> [AZURE.NOTE] Para la resistencia frente a los errores de hardware, la Alta disponibilidad implica disponer de varias máquinas virtuales en una sola unidad BizTalk.


## Preguntas más frecuentes

#### ¿Qué es una unidad BizTalk?
Una "unidad" es el nivel atómico de una implementación de Servicios de BizTalk de Azure. Cada edición incluye una unidad con distinta memoria y capacidad de proceso. Por ejemplo, una unidad Basic tiene más proceso que Developer, Standard tiene más proceso que Basic, etc. Cuando escala un servicio de BizTalk, se escala en términos de unidades.

#### ¿Qué diferencia hay entre Servicios de BizTalk y una máquina virtual de BizTalk de Azure?
Servicios de BizTalk proporciona una verdadera arquitectura de plataforma como servicio (PaaS) para la creación de soluciones de integración en la nube. Con el modelo de PaaS, se puede centrar por completo en la lógica de la aplicación y dejar toda la administración de la infraestructura a Microsoft, incluyendo lo siguiente:

- No hay necesidad de administrar o revisar máquinas virtuales.
- Microsoft garantiza la disponibilidad.
- Puede controlar la escala a petición simplemente solicitando más o menos capacidad a través del portal de Azure.

BizTalk Server en Máquinas virtuales de Azure proporciona una arquitectura de infraestructura como servicio (IaaS). Puede crear máquinas virtuales y configurarlas exactamente como el entorno local, con lo que se facilita la ejecución de aplicaciones existentes en la nube sin cambiar el código. Con IaaS, sigue siendo su responsabilidad la configuración de las máquinas virtuales, la administración de las máquinas virtuales (por ejemplo, la instalación de software y revisiones de sistema operativo) y el diseño de la aplicación para una alta disponibilidad.

Si busca crear nuevas soluciones de integración que minimicen su esfuerzo de administración de la infraestructura, utilice Servicios de BizTalk. Si busca migrar rápidamente sus soluciones existentes de BizTalk o desea un entorno a petición para desarrollar y probar las aplicaciones de BizTalk Server, utilice BizTalk Server en la Máquina virtual de Azure.

#### ¿Cuál es la diferencia entre el Servicio de adaptador de BizTalk y las conexiones híbridas?
El Servicio de adaptador de BizTalk se usa por un Servicio de BizTalk de Azure. El servicio de adaptador de BizTalk usa el Pack de adaptador de BizTalk para establecer una conexión con un sistema de línea de negocio (LOB) local. Una conexión híbrida ofrece una manera sencilla y adecuada de conectar aplicaciones de Azure, como la característica de Aplicaciones web en el Servicio de aplicaciones de Azure y Servicios móviles de Azure, a un recurso local.

#### Significado de la transferencia de datos de conexiones híbridas (GB) por unidad ¿Es por minuto/hora/día/semana/mes? ¿Qué ocurre cuando se alcanza el límite?

El costo de conexión híbrida por unidad depende de la edición de los Servicios de BizTalk. En pocas palabras, los costos dependen de la cantidad de datos que transfiera. Por ejemplo, transferir 10 GB de datos al día cuesta menos que transferir 100 GB al día. Utilice la [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/?scenario=full) para que los Servicios de BizTalk determinen los costos específicos. Normalmente, los límites se aplican diariamente. Si supera el límite, cualquier cobertura se cargará a un precio de $1 por GB.

#### Cuando creo un contrato en Servicios de BizTalk, ¿por qué el número de puentes sube en incrementos de dos, en lugar de solo uno?

Cada contrato consta de dos puentes distintos: un puente de comunicación de envío y un puente de comunicación de recepción.

####  ¿Qué ocurre cuando se alcanza el límite en la cuota del número de puentes o contratos?

No podrá implementar ningún otro puente ni crear otro acuerdo. Si desea implementar más, deberá escalar verticalmente las unidades del servicio de BizTalk o actualizar a una edición superior.

#### ¿Cómo realizo la migración de un nivel de Servicios de BizTalk a otro?

La edición gratuita no se puede migrar ni "escalar verticalmente" a otro nivel y tampoco es posible realizar una copia de seguridad y restaurarla a otro nivel. Si necesita otro nivel, cree un nuevo servicio de BizTalk que use el nuevo nivel. Todos los artefactos creados con la edición gratuita, incluidas las conexiones híbridas, deben volver a crearse en el nuevo servicio de BizTalk.

En las ediciones restantes, utilice la copia de seguridad y restauración para migrar los artefactos de un nivel a otro. Por ejemplo, realice una copia de seguridad de los artefactos en el nivel estándar y restáurelos en el nivel Premium. [Servicios de BizTalk: copias de seguridad y restauración](biztalk-backup-restore.md) describe las rutas de acceso de migración compatibles y enumera los artefactos de los que se realiza copia de seguridad. Tenga en cuenta que no se realiza copia de seguridad de las conexiones híbridas. Después de realizar una copia de seguridad y restaurarla en un nuevo nivel, debe volver a crear las conexiones híbridas.


#### ¿El servicio de adaptador de BizTalk está incluido en el servicio? ¿Cómo puedo recibir el software?

Sí, el Servicio de adaptador de BizTalk con BizTalk Adapter Pack está incluido en la [descarga](http://www.microsoft.com/download/details.aspx?id=39087) del SDK de Servicios de BizTalk de Azure.

## Pasos siguientes

Para crear los Servicios de BizTalk de Azure en el Portal de Azure, vaya a [Creación de Servicios de BizTalk mediante el Portal de Azure](biztalk-provision-services.md). Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Recursos adicionales
- [Creación de Servicios de BizTalk mediante el Portal de Azure](biztalk-provision-services.md)<br/>
- [Servicios de BizTalk: gráfico del estado de aprovisionamiento](biztalk-service-state-chart.md)<br/>
- [Servicios de BizTalk: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Servicios de BizTalk: copias de seguridad y restauración](biztalk-backup-restore.md)<br/>
- [Servicios de BizTalk: limitaciones](biztalk-throttling-thresholds.md)<br/>
- [Servicios de BizTalk: nombre del emisor y clave del emisor](biztalk-issuer-name-issuer-key.md)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!---HONumber=AcomDC_0302_2016-->
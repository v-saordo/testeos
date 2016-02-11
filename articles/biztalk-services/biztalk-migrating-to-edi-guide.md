<properties   
	pageTitle="Guía técnica de migración de soluciones de EDI de BizTalk Server a Servicios de BizTalk | Microsoft Azure"
	description="Migración de EDI a MABS; Servicios de BizTalk de Microsoft Azure"
	services="biztalk-services"
	documentationCenter="na"
	authors="MandiOhlinger"
	manager="dwrede"
	editor="dwrede"/>

<tags 
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mandia"/>


# Migración de soluciones de EDI de BizTalk Server a Servicios de BizTalk: Guía técnica

Autor: Tim Wieman y Nitin Mehrotra

Revisores: Karthik Bharthy

Escrito utilizando: la versión de febrero de 2014 de Servicios de BizTalk de Microsoft Azure..

## Introducción

Intercambio electrónico de datos (EDI) es uno de los medios más frecuentes mediante los que las empresas intercambian datos electrónicamente. También se llaman transacciones entre empresas o B2B. BizTalk Server lleva teniendo compatibilidad con EDI más de una década, desde la versión inicial de BizTalk Server. Con Servicios de BizTalk, Microsoft continúa la compatibilidad con las soluciones EDI en la Plataforma Microsoft Azure. Las transacciones B2B son mayormente externas a una organización y, por tanto, resultan más fáciles de implementar si se han implementado en una plataforma en la nube. Microsoft Azure proporciona esta capacidad a través de Servicios de BizTalk.

Aunque algunos clientes consideran Servicios de BizTalk como una plataforma "virgen" para nuevas soluciones EDI, muchos clientes tienen soluciones EDI de BizTalk Server actuales que pueden migrar a Azure. Porque EDI de Servicios de BizTalk está diseñado según las mismas entidades clave que la arquitectura de EDI de BizTalk Server (socios comerciales, entidades, contratos), es posible migrar artefactos de EDI de BizTalk Server a los Servicios de BizTalk.

Este documento explica algunas de las diferencias de migrar los artefactos de EDI de BizTalk Server a los Servicios de BizTalk. En este documento se da por hecho que se dispone de un conocimiento práctico del procesamiento de EDI de BizTalk Server y de los contratos de socios comerciales. Para obtener más información sobre EDI de BizTalk Server, consulte [Administración de socios comerciales mediante BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## ¿Qué versión de los artefactos de EDI de BizTalk Server se puede migrar a Servicios de BizTalk?

El módulo de EDI de BizTalk Server se ha mejorado significativamente para BizTalk Server 2010, cuando se rediseñó para incluir socios, perfiles y contratos. Servicios de BizTalk usa el mismo modelo para organizar los socios comerciales y las divisiones de negocio situadas dentro de esos socios comerciales. Como resultado, la migración de artefactos EDI de BizTalk Server 2010 y versiones posteriores a los Servicios de BizTalk es un proceso mucho más sencillo. Para migrar los artefactos EDI asociados a las versiones anteriores de BizTalk Server 2010, debe actualizar primero a BizTalk Server 2010 y, a continuación, migrar los artefactos de EDI a los Servicios de BizTalk.

## Escenarios y flujo de mensajes

Como con BizTalk Server, el procesamiento de EDI en Servicios de BizTalk se basa en una solución de Administración de socios comerciales (TPM). La solución TPM tiene los siguientes componentes clave:

- Los socios comerciales, que representan una organización en una transacción B2B.
- Los perfiles, que representan divisiones dentro de un socio comercial.
- Contratos de socio comercial (o contratos), que representan el acuerdo comercial entre dos socios o perfiles.

En la siguiente ilustración se muestran las similitudes, así como las diferencias, entre una solución de EDI de BizTalk Server y una solución EDI de BizTalk Services:

![][EDImessageflow]

Las similitudes y diferencias clave entre un flujo de solución EDI en BizTalk Server y los Servicios de BizTalk son:

- Al igual que BizTalk Server usa una canalización EDIReceive para recibir un mensaje EDI y una canalización EDISend para enviar un mensaje EDI, Servicios de BizTalk usa un puente de recepción EDI para recibir y un puente de envío EDI para enviar mensajes EDI. En BizTalk Server, las canalizaciones se asocian con un acuerdo mediante el uso de puertos de envío o de recepción. En Servicios de BizTalk, el propio contrato denota el puente de envío o recepción.
- En BizTalk Server, una vez que la canalización EDIReceive procesa el mensaje EDI, el mensaje se vuelca en una base de datos de SQL Server. A continuación, la canalización EdiSend toma el mensaje de la base de datos de SQL Server, lo procesa y, a continuación, lo envía al socio comercial.

	En Servicios de BizTalk, después de que el puente de recepción de EDI procesa el mensaje EDI, enruta el mensaje a un proceso externo. El proceso externo podría ejecutarse en Microsoft Azure o de forma local. El proceso externo debe enrutar el mensaje al puente de envío EDI; el puente de envío no lo extrae intrínsecamente. Después de procesar el mensaje, el puente de envío EDI enruta el mensaje al socio comercial.

Servicios de BizTalk proporciona una experiencia de configuración fácil de usar para crear e implementar rápidamente un contrato B2B entre socios comerciales sin configurar cualquier instancia de proceso de Microsoft Azure (roles web o de trabajo), las Bases de datos SQL de Microsoft Azure o las cuentas de Almacenamiento de Microsoft Azure. Escenarios más complejos requerirán enlazar flujos de trabajo u otro procesamiento de servicio "en torno a los bordes" de un acuerdo de socio comercial, es decir, antes o después del procesamiento de puente EDI de contrato de socio comercial. Con mayor detalle, las siguientes secuencias de eventos se producen durante un procesamiento de mensaje EDI en los Servicios de BizTalk.

1. Se recibe un mensaje EDI del socio comercial, Fabrikam. Para recibir mensajes EDI de socios comerciales, los Servicios de BizTalk admiten protocolos de transporte como FTP, SFTP, AS2 y HTTP/S.

2. El procesamiento del lado de recepción del contrato de socio comercial desensambla el mensaje EDI en formato XML. Puede enrutar el mensaje EDI desensamblado (en formato XML) a los extremos del Bus de servicio como un extremo de retransmisión de bus de servicio, tema de bus de servicio, cola de bus de servicio o un puente de Servicios de BizTalk.

3. Los mensajes XML desensamblados podrán recibirse entonces desde el extremo para efectuar un procesamiento más personalizado. Estos extremos podrán ser procesados por un componente local o una instancia de proceso de Microsoft Azure para procesar el mensaje en un servicio de flujo de trabajo de Windows (WF) o Windows Communication Foundation (WCF), por ejemplo.

4. El "procesamiento de envío" del contrato del socio comercial ensambla a continuación el mensaje XML en formato EDI y lo envía al socio comercial, Contoso. Para enviar mensajes EDI a socios comerciales, los Servicios de BizTalk admiten los mismos protocolos que los usados para recibir los mensajes EDI.

Este documento proporciona instrucciones conceptuales sobre la migración de algunos de los diferentes artefactos de EDI de BizTalk Server para los Servicios de BizTalk.

## Puertos de envío/recepción para socios comerciales

En BizTalk Server se configuran ubicaciones de recepción y puertos de recepción para recibir mensajes EDI/XML de socios comerciales y configurar puertos de envío para enviar los mensajes EDI/XML al socio comercial. A continuación se asocian estos puertos a un acuerdo entre socios comerciales mediante la consola de administración de BizTalk Server. En Servicios de BizTalk, las ubicaciones en las que recibe los mensajes de los socios comerciales y donde envía mensajes a los socios comerciales se configuran como parte del acuerdo entre socios comerciales (como parte de la configuración de transporte) en el Portal de Servicios de BizTalk. Por lo tanto, realmente no se tiene el concepto de "puertos de envío" y "ubicaciones de recepción" en sí en Servicios de BizTalk. Para obtener más información, consulte [Creación de contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## Canalizaciones (puentes)

En EDI de BizTalk Server, las canalizaciones son entidades de procesamiento de mensajes que también pueden incluir una lógica personalizada para capacidades de procesamiento específicas, tal como requiere la aplicación. Para los Servicios de BizTalk, el equivalente sería un puente EDI. Sin embargo, en Servicios de BizTalk, por ahora, los puentes EDI están "cerrados". Es decir, no puede agregar sus propias actividades personalizadas a un puente EDI. Cualquier procesamiento personalizado debe hacerse fuera el puente EDI en la aplicación, ya sea antes o después de que el mensaje acceda al puente configurado como parte del contrato de socio comercial. Los puentes EAI tienen la opción de hacer un procesamiento personalizado. Si desea efectuar el procesamiento personalizado, puede usar puentes EAI antes o después de que el mensaje sea procesado por el puente EDI. Para obtener más información, consulte [Cómo incluir código personalizado en puentes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Puede insertar un flujo de publicación/suscripción con código personalizado y/o usar colas y temas de mensajería de bus de servicio antes de que el contrato de socio comercial reciba el mensaje, o después de que el acuerdo procese el mensaje y lo enrute a un extremo del Bus de servicio.

Consulte **Escenarios y flujo de mensajes** en este tema para obtener el modelo de flujo de mensajes.

## Contratos

Si está familiarizado con los contratos de socio comercial de BizTalk Server 2010 usados para el procesamiento de EDI, los contratos de socios comerciales de Servicios de BizTalk le resultarán conocidos. La mayor parte de la configuración del acuerdo es la misma y usa la misma terminología. En algunos casos, la configuración del contrato es mucho más simple en comparación con la misma configuración de BizTalk Server. Servicios de BizTalk de Microsoft Azure es compatible con el transporte X12, EDIFACT y AS2.

Servicios de BizTalk de Microsoft Azure también proporciona una herramienta de **migración de datos de TPM** para migrar socios comerciales y contratos del módulo de socios comerciales de BizTalk Server al portal de servicios de BizTalk. La herramienta de migración de datos de TPM está disponible como parte de un paquete de herramientas, que puede descargarse del [SDK de MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057). El paquete también incluye un archivo Léame que proporciona instrucciones sobre cómo usar la herramienta e información de solución de problemas básica de la herramienta.

## Esquemas

Servicios de BizTalk proporciona esquemas EDI que se pueden usar en soluciones de Servicios de BizTalk. Además, los esquemas EDI de BizTalk Server también se pueden usar con Servicios de BizTalk porque el nodo raíz del esquema EDI es el mismo en BizTalk Server y en los Servicios de BizTalk. De este modo, podrá tomar los esquemas EDI de BizTalk Server directamente y usarlos en las soluciones EDI que desarrolle mediante Servicios de BizTalk. También puede descargar los esquemas del [SDK MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## Asignaciones (transformaciones)

Las asignaciones en BizTalk Server se llaman transformaciones en Servicios de BizTalk. Migrar asignaciones de BizTalk Server a Servicios de BizTalk puede ser una de las tareas más complejas para realizar (según la complejidad de la asignación). La herramienta de asignación que se utiliza para los Servicios de BizTalk es diferente del asignador de BizTalk. Aunque el asignador parece casi igual, el formato de asignación subyacente es diferente. Los functoids (denominados **Operaciones de asignación** en Servicios de BizTalk) disponibles para los usuarios también son diferentes. De hecho, no se puede usar directamente una asignación de BizTalk en Servicios de BizTalk. Además, no todos los functoids disponibles en BizTalk Server están disponibles como operaciones de asignación en Servicios de BizTalk.

### Nuevas operaciones de transformación

Aunque la lista de operaciones de asignación de transformación disponibles pueden ser muy diferentes de las del asignador de BizTalk Server, las transformaciones de Servicios de BizTalk tienen formas nuevas de realizar las mismas tareas. Por ejemplo, las transformaciones de Servicios de BizTalk tienen **operaciones de lista** disponibles. Esto no estaba disponible en el asignador de BizTalk. Las **operaciones de lista** le permiten crear y operar en una "Lista", donde las listas son un conjunto de elementos (también conocido como "filas") y cada elemento puede tener varios miembros (también conocidos como "columnas"). Puede ordenar la lista, seleccionar elementos según una condición, etc.

Otro ejemplo de la nueva funcionalidad de las transformaciones de Servicios de BizTalk son las **operaciones de bucle**. Es difícil crear bucles anidados en el asignador de BizTalk Server. Por lo tanto, las operaciones de asignación de bucle se agregan para las transformaciones de Servicios de BizTalk.

Otro ejemplo es la operación de asignación de expresión **If-Then-Else**. En el asignador BizTalk era posible realiza una operación de if-then-else, pero requería que varios functoids realizasen una tarea aparentemente simple.

### Migración de asignaciones de BizTalk Server

Servicios de BizTalk de Microsoft Azure proporciona una herramienta de migración de asignaciones de BizTalk Server a transformaciones de Servicios de BizTalk. La **BTMMigrationTool** está disponible como parte del paquete **Herramientas** proporcionado con la [descarga del SDK de Servicios de BizTalk](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para obtener más información acerca de la herramienta, consulte [Convertir una asignación de BizTalk en una transformación de servicios de BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

También puede ver un ejemplo realizado por Sandro Pereira, MVP de BizTalk, sobre cómo [migrar asignaciones de BizTalk Server a transformaciones de los Servicios de BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## Orquestaciones

Si necesita migrar a Microsoft Azure el procesamiento de la orquestación de BizTalk Server, las orquestaciones tendrían que reescribirse porque Microsoft Azure no admite la ejecución de las orquestaciones de BizTalk Server. Puede volver a escribir la funcionalidad de orquestación en un servicio de Windows Workflow Foundation 4.0 (WF4). Esto sería una reescritura completa, ya que no hay actualmente ninguna migración desde orquestaciones de BizTalk Server a WF4. Estos son algunos recursos para el flujo de trabajo de Windows:

- [*Cómo integrar un servicio de flujo de trabajo de WCF con temas y colas de Bus de servicio*](https://msdn.microsoft.com/library/azure/hh709041.aspx), por Paolo Salvatori. 

- [*Generación de aplicaciones con una sesión de Windows Workflow Foundation y Azure* ](http://go.microsoft.com/fwlink/p/?LinkId=237314)desde la compilación 2011.

- [*Centro de desarrolladores de Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) en MSDN.

- [*Documentación de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) en MSDN.

## Otras consideraciones

A continuación se facilitan algunas consideraciones que debe realizar durante el uso de Servicios de BizTalk.

### Contratos de reserva

El procesamiento de EDI de BizTalk Server tiene el concepto de "contratos de reserva". Servicios de BizTalk **no** tiene un concepto de contrato de reserva hasta ahora. Vea los temas de la documentación de BizTalk [El rol de los contratos en el procesamiento de EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) y [Configuración de las propiedades del contrato global o de reserva](https://msdn.microsoft.com/library/bb245981.aspx) para información sobre cómo se usan los acuerdos de reserva en BizTalk Server.

### Redirección a múltiples destinos

Los puentes de Servicios de BizTalk, en su estado actual, no admiten enrutamiento de mensajes a varios destinos mediante un modelo publicación-suscripción. En su lugar se podrían enrutar mensajes de un puente de Servicios de BizTalk a un tema de Bus de servicio, que puede tener varias suscripciones para recibir el mensaje en más de un extremo.

## Conclusión

Servicios de BizTalk de Microsoft Azure se actualiza a intervalos regulares para agregar más características y capacidades. Con cada actualización, esperamos admitir una mayor funcionalidad para facilitar la creación de soluciones integrales con Servicios de BizTalk y otras tecnologías de Azure.

## Otras referencias

[Desarrollo de aplicaciones empresariales con Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png

<!---HONumber=AcomDC_1223_2015-->
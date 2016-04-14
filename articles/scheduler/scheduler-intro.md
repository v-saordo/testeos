<properties
 pageTitle="¿Qué es el Programador de Azure? | Microsoft Azure"
 description="Programador de Azure le permite describir mediante declaración las acciones para ejecutar en la nube. A continuación, programa y ejecuta esas acciones de forma automática."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# ¿Qué es el Programador de Azure?

Programador de Azure le permite describir mediante declaración las acciones para ejecutar en la nube. A continuación, programa y ejecuta esas acciones de forma automática. Para hacerlo, Programador usa el código del [Portal de Azure](scheduler-get-started-portal.md), la [API de REST](https://msdn.microsoft.com/library/dn528946) o Azure PowerShell.

El Programador crea, mantiene e invoca el trabajo programado. El Programador no hospeda ninguna carga de trabajo ni ejecuta código. Solo _invoca_ código hospedado en cualquier otro lugar (en Azure, localmente o con otro proveedor). Invoca a través de HTTP, HTTPS o una cola de almacenamiento.

El Programador programa [trabajos](scheduler-concepts-terms.md), mantiene un historial de los resultados de la ejecución de trabajos que se puede consultar y programa, de manera determinante y confiable, las cargas de trabajo que se ejecutarán. Azure WebJobs (parte de la característica de Aplicaciones web del Servicio de aplicaciones de Azure) y otras funcionalidades de programación de Azure usan el Programador en segundo plano. La [API de REST de Programador](https://msdn.microsoft.com/library/dn528946) ayuda a administrar la comunicación para estas acciones. De ese modo, el Programador admite [programaciones complejas y periodicidad avanzada](scheduler-advanced-complexity.md) con facilidad.

Existen varios escenarios que se prestan para el uso del Programador. Por ejemplo:

+ _Acciones de aplicaciones recurrentes_: recopilación periódica de datos desde Twitter en una fuente.
+ _Mantenimiento diario_: eliminación diaria de registros, realización de copias de seguridad y otras tareas de mantenimiento. Por ejemplo, un administrador puede optar por hacer una copia de seguridad de su base de datos a la 1:00 a.m. todos los días durante los próximos 9 meses.

Programador permite crear, actualizar, eliminar, ver y administrar [colecciones de trabajos y trabajos](scheduler-concepts-terms.md) de manera programática, mediante scripts y en el portal.

## Consulte también

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0128_2016-->
<properties
   pageTitle="Preguntas frecuentes relacionadas con la retirada de las versiones Web y Business | Microsoft Azure"
   description="Obtenga información sobre cuándo se retirarán las bases de datos SQL de Azure Web y Business y sobre las características y funciones de los nuevos niveles de servicio."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jeffreyg"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/23/2016"
   ms.author="sstein" />

# Preguntas frecuentes relacionadas con la retirada de las versiones Web y Business

Las bases de datos Web y Business de Azure se han retirado. Los niveles Basic, Standard, Premium y Elastic son los que reemplazan las bases de datos Web y Business.

Para ayudarle con la actualización de las bases de datos Web y Business, el servicio de bases de datos de SQL recomienda un nivel de servicio apropiado y un nivel de rendimiento (nivel de precios) para cada base de datos basado en la carga de trabajo histórica.

**Obtención de recomendaciones de nivel de precios:**

- [Actualización a la base de datos SQL V12 con el Portal de Azure](sql-database-upgrade-server-portal.md)
- [Actualización a la base de datos SQL V12 con PowerShell](sql-database-upgrade-server-powershell.md)
- [Cambio del nivel de precios de una base de datos Web o Business](sql-database-service-tier-advisor.md)



## ¿Por qué muestra el Portal de Azure mis bases de datos Web y Business como retiradas?

Puesto que las versiones Web y Business de las bases de datos dejarán de estar disponibles después de septiembre de 2015, el portal etiqueta las bases de datos Web y Business como retiradas. La etiqueta Retirado también avisa de que se deben actualizar las bases de datos Web y Business a Premium, Basic o Standard. Para obtener información detallada sobre cómo actualizar las bases de datos Web o Business existentes a los nuevos niveles de servicio, consulte [Actualización a Base de datos SQL de Azure V12](sql-database-upgrade-server-portal.md).

## ¿Qué nuevo nivel de servicio es la mejor opción para actualizar la base de datos Web o Business existente?

Seleccionar un nuevo nivel de servicio y un nuevo nivel de rendimiento para la base de datos Web o Business existente depende de los requisitos específicos de características y rendimiento de su aplicación.

Use las recomendaciones de plan de tarifa descritas anteriormente, o bien consulte [Actualización a Base de datos SQL de Azure V12](sql-database-upgrade-server-portal.md) para obtener información detallada que le ayudará a seleccionar un nivel de servicio adecuado.

## ¿Por qué introduce Microsoft nuevos niveles de servicio?

Según los comentarios de los clientes, se van a introducir nuevos niveles de servicio de base de datos SQL de Azure para ayudar a los clientes a admitir las cargas de trabajo de bases de datos relacionales. Los nuevos niveles están diseñados para ofrecer un rendimiento predecible en un espectro de siete niveles para todo tipo de demandas de aplicaciones transaccionales: desde demandas ligeras hasta las más intensas. Además, los nuevos niveles ofrecen una amplia gama de características de continuidad empresarial, un mayor tiempo de actividad de contrato de nivel de servicio, mayores tamaños de base de datos con menos costes y una mejor experiencia de facturación.

## ¿Dónde puedo obtener más información sobre los nuevos niveles de servicio?

Para obtener información detallada sobre los nuevos niveles de servicio y sobre el modelo de rendimiento, consulte [Niveles de servicio](sql-database-service-tiers.md). Para obtener información detallada sobre los precios de los nuevos niveles de servicio, consulte [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## ¿Qué características o funciones no estarán disponibles en los niveles Basic, Standard y Premium?

La característica de federaciones se retirará con las versiones Web y Business. Se recomienda a los clientes que necesiten escalar horizontalmente sus bases de datos usar las [Herramientas de bases de datos flexibles](sql-database-elastic-scale-get-started.md) para las [base de datos SQL de Azure](sql-database-elastic-scale-get-started.md), o migrar a dichas herramientas, ya que simplifican la creación y administración de aplicaciones que usan particionamiento. Una biblioteca de cliente .NET permite a las aplicaciones definir cómo se asignan los datos a las particiones y enruta las solicitudes OLTP a las bases de datos correctas. Para admitir las operaciones de administración que permiten reconfigurar el modo en que se distribuyen los datos entre particiones, se incluye una plantilla de servicio de nube de Azure que puede hospedar en su propia suscripción de Azure. Además de las [Herramientas de bases de datos flexibles](sql-database-elastic-scale-get-started.md), Microsoft seguirá creando y publicando [directrices de prácticas y patrones de particionamiento personalizado](https://msdn.microsoft.com/library/azure/dn764977.aspx) basadas en el aprendizaje obtenido con importantes compromisos de clientes. Los nuevos clientes que necesiten la funcionalidad de escalado horizontal deberán consultar la información sobre [Herramientas de bases de datos flexibles](sql-database-elastic-scale-get-started.md) o ponerse en contacto con el Soporte técnico de Microsoft para evaluar sus opciones.

Microsoft también va a cambiar la experiencia de copia de la base de datos con las bases de datos Premium. Anteriormente, puesto que la cuota de las bases de datos Premium era limitada, CREATE DATABASE … AS A COPY OF en T-SQL creaba una base de datos Premium suspendida sin recursos reservados que se cargaba a la misma velocidad que una base de datos Business. Puesto que ahora hay más cuota Premium disponible de manera gratuita, ya no se admite la base de datos Premium suspendida. Las copias de las bases de datos ahora se crearán con la misma versión y nivel de rendimiento que el origen y se facturarán según corresponda. Los clientes pueden optar por degradar las bases de datos copiadas a un nivel de servicio o de rendimiento distintos para reducir su coste, si así lo desean. Las bases de datos Premium suspendidas existentes se convertirán a la versión Business como parte de esta versión. Se prevé que la introducción del punto de restauración a un momento dado reducirá la necesidad de realizar copias de seguridad de las bases de datos.

## ¿Cómo pueden los niveles Basic, Standard y Premium mejorar la mi experiencia de facturación?

Los niveles de bases de datos SQL de Azure Basic, Standard y Premium se facturan por hora y es posible escalar o reducir cada base de datos 4 veces en un período de 24 horas. Se facturará una tarifa fija basada en el nivel de servicio y rendimiento más altos elegidos para cada hora. Además, los niveles de rendimiento (ejemplo: Basic, S1 y P2) se desglosan en la factura para que resulte más fácil ver el número de días y horas de base de datos incurridos en un mes en cada nivel de rendimiento. Las bases de datos Web y Business se siguen facturando con unidades de base de datos en función del tamaño de la base de datos. Visite la [página de precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) para obtener más información sobre los precios y las diferencias entre los nuevos niveles de servicio.


## Consulte también

[Base de datos SQL de Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Precios de la edición Web y Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Niveles de servicio](sql-database-service-tiers.md)

[Actualización a Base de datos SQL de Azure V12](sql-database-upgrade-server-portal.md)

<!---HONumber=AcomDC_0224_2016-->
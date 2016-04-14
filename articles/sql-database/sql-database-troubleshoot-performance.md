<properties
	pageTitle="Solucione problemas de rendimiento de bases de datos con la Base de datos SQL de Azure."
	description="Pasos rápidos para solucionar problemas de rendimiento de la base de datos."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="v-shysun"/>

# Solucionar problemas de rendimiento de bases de datos con la Base de datos SQL de Azure
Puede cambiar el [nivel de servicio](sql-database-service-tiers.md) de una base de datos única o aumentar las eDTU de un grupo de bases de datos elásticas en cualquier momento para mejorar el rendimiento, pero es posible que quiera identificar las oportunidades para mejorar y optimizar el rendimiento de las consultas primero. Entre los motivos comunes para un rendimiento deficiente de la base de datos se encuentran índices que faltan y consultas mal optimizadas.

## Pasos para evaluar y ajustar el rendimiento de bases de datos
1.	En el [Portal de Azure](https://portal.azure.com), haga clic en **Bases de datos SQL**, seleccione la base de datos y luego use el gráfico de supervisión para buscar recursos que se acerquen a su máximo. El consumo de DTU se muestra de manera predeterminada. Haga clic en **Editar** para cambiar el intervalo de tiempo y los valores que se muestran.
2.	Use [Query Performance Insight](sql-database-query-performance.md) para evaluar las consultas usando DTU y luego use el [Asesor de índices](sql-database-index-advisor.md) para recomendar y crear índices.
3.	Puede usar vistas de administración dinámica (DMV), eventos extendidos (Xevents) y el almacén de consultas en SSMS para obtener parámetros de rendimiento en tiempo real. Vea el [tema de directrices sobre el rendimiento](sql-database-performance-guidance.md) para la supervisión detallada y sugerencias de optimización.

## Pasos para mejorar el rendimiento de la base de datos con más recursos
1.	Para las bases de datos únicas, puede [cambiar los niveles de servicio](sql-database-scale-up.md) a petición para mejorar el rendimiento de la base de datos.
2.	Para varias bases de datos, considere el uso de [grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para escalar recursos automáticamente.

Si los problemas de rendimiento continúan, póngase en contacto con el soporte técnico para abrir un caso de soporte técnico.

<!---HONumber=AcomDC_1217_2015-->
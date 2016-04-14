<properties 
	pageTitle="Novedades de Base de datos SQL V12 | Microsoft Azure" 
	description="Describe cuáles son las ventajas que van a obtener los sistemas empresariales que están utilizando Base de datos SQL de Azure en la nube al actualizarse a la versión V12 ahora." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="genemi"/>


# Novedades de la Base de datos de SQL V12


En este tema se describen las numerosas ventajas de la nueva versión V12 de Base de datos SQL de Azure con respecto a la versión V11.


Seguimos agregando características a la V12. Por lo tanto, le recomendamos que visite nuestra página web Actualizaciones del servicio para Azure y que use los filtros:


- Filtrado a [Servicio de Base de datos SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado a Disponibilidad general [Anuncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para características de Base de datos SQL.


La información más reciente sobre los límites de recursos de Base de datos SQL de Azure se documenta en:<br/>[Límites de recursos de Base de datos SQL de Azure](sql-database-resource-limits.md).


## Compatibilidad de aplicaciones aumentada con SQL Server


Un objetivo fundamental de Base de datos SQL V12 era mejorar la compatibilidad con Microsoft SQL Server 2014. Entre otros ámbitos, V12 logra la paridad con SQL Server en el importante campo de la programación. Por ejemplo:


- [Funciones de ventana](http://msdn.microsoft.com/library/bb934097.aspx), con [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 
- [Índices XML](http://msdn.microsoft.com/library/bb934097.aspx) e [Índices XML selectivos](http://msdn.microsoft.com/library/jj670104.aspx)
- [Seguimiento de cambios](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [Búsqueda de texto completo](http://msdn.microsoft.com/library/ms142571.aspx)


Consulte [aquí](sql-database-transact-sql-information.md) para ver el pequeño conjunto de características que aún no se admite en Base de datos SQL.


## Más rendimiento Premium, nuevos niveles de rendimiento


En V12, aumentamos las unidades de transacción de base de datos (DTU) asignadas a todos los niveles de rendimiento Premium en un 25 % sin ningún costo adicional. Se consiguen incluso mejoras de rendimiento mayores con nuevas características como las siguientes:


- Compatibilidad para [índices de almacén de columnas](http://msdn.microsoft.com/library/gg492153.aspx) en memoria.
- [Particiones de tablas por filas](http://msdn.microsoft.com/library/ms187802.aspx) gracias a mejoras relacionadas con [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- Disponibilidad de vistas de administración dinámica [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) para ayudar a supervisar y optimizar el rendimiento.


### Rendimiento confiable


Si su programa cliente se conecta a Base de datos SQL V12, mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir los siguientes intervalos de puerto en dicha VM:

- 11000-11999
- 14000-14999


Haga clic [aquí](sql-database-develop-direct-route-ports-adonet-v12.md) para obtener más información acerca de los puertos para Base de datos SQL V12. Los puertos son necesarios por las mejoras de rendimiento en Base de datos SQL V12.


## Mejor compatibilidad para proveedores de SaaS en la nube


Solo en V12, hemos lanzado el nuevo nivel de rendimiento estándar S3 y la versión preliminar pública de [grupos de bases de datos elásticas](sql-database-elastic-pool.md). Se trata de una solución diseñada específicamente para proveedores de SaaS en la nube. Con grupos de bases de datos elásticas, puede:


- Compartir DTU entre bases de datos para reducir costos cuando se tiene un gran número de bases de datos.
- Ejecutar [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md) para administrar bases de datos a escala.


## Mejoras de seguridad


La seguridad es una cuestión prioritaria para cualquiera que dirija su negocio en la nube. Las características de seguridad más recientes publicadas en V12 incluyen:


- [Seguridad de nivel de fila](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Enmascaramiento de datos dinámicos](sql-database-dynamic-data-masking-get-started.md)
- [Bases de datos independientes](http://msdn.microsoft.com/library/ff929188.aspx)
- [Roles de aplicación](http://msdn.microsoft.com/library/ms190998.aspx) administrados con GRANT, DENY, REVOKE
- [Cifrado de datos transparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md)
 - Base de datos SQL ahora admite la autenticación de Azure Active Directory, un mecanismo de conexión a Base de datos SQL utilizando identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure Active Directory puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central.
- [Siempre cifrado](https://msdn.microsoft.com/library/mt163865.aspx) (versión preliminar) hace que el cifrado sea transparente para las aplicaciones y permite que los clientes cifren información confidencial dentro de aplicaciones cliente sin compartir las claves de cifrado con Base de datos SQL.


## Mayor continuidad del negocio cuando se necesita recuperación


V12 ofrece una mejora significativa en lo que a objetivos de puntos de recuperación (RPO) y tiempos de recuperación estimados (ERT) se refiere:


| Característica de continuidad del negocio | Versión anterior | V12 |
| :-- | :-- | :-- |
| Restauración geográfica | • RPO < 24 horas.<br/>• ERT < 12 horas. | • RPO < 1 hora.<br/>• ERT < 12 horas. |
| Replicación geográfica estándar | • RPO < 30 minutos.<br/>• ERT < 2 horas. | • RPO < 5 segundos.<br/>• ERT < 30 segundos. |
| Replicación geográfica activa | • RPO < 5 minutos.<br/>• ERT < 1 hora. | • RPO < 5 segundos.<br/>• ERT < 30 segundos. |


Para obtener más información, consulte [Continuidad del negocio en Base de datos SQL](sql-database-business-continuity.md).


## Más razones para actualizar ahora


Hay muchas buenas razones por las que los clientes deberían actualizar ahora Base de datos SQL de Azure de la V11 a la V12:


- V12 de Base de datos SQL presenta una larga lista de características que superan a las de la V11.
- Seguimos agregando nuevas características a la V12, pero ya no vamos a agregar más características a la V11.
- La mayoría de las características nuevas se lanzan en Base de datos SQL V12 antes que en Microsoft SQL Server.


## ¿Ya utiliza V12?


Una forma fácil de ver si tiene una base de datos o un servidor lógico que se ejecuta en una versión anterior del servicio de Base de datos SQL es hacer lo siguiente:


1. Vaya al [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**.
3. Haga clic en **Servidores SQL Server**.
4. El icono al lado del servidor o la base de datos refleja el historial:
 - ![Icono para un servidor v12](./media/sql-database-v12-whats-new/v12_icon.png) **Servidor lógico de V12**
 - ![Icono para un servidor de una versión anterior](./media/sql-database-v12-whats-new/earlier_icon.png) **Servidor lógico de versiones anteriores**


Otra técnica para determinar la versión es ejecutar la instrucción `SELECT @@version;` en la base de datos y ver resultados similares a los siguientes:


- **12**.0.2000.10 &nbsp; *(versión V12)*
- **11**.0.9228.18 &nbsp; *(versión V11)*


Una base de datos V12 solo puede hospedarse en un servidor lógico V12. Y un servidor V12 puede hospedar solo bases de datos V12.


Si aún no está ejecutando en V12, puede actualizar el servidor lógico siguiendo los pasos descritos en [Actualización a Base de datos SQL de Azure V12 in situ](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a> Regiones de disponibilidad general


- Para el 31 de julio de 2015, había ascendido ya a Disponibilidad general (GA) en todas las regiones.
- V12 se lanzó en diciembre de 2014 pero solo en el estado de Vista previa.

[Términos de uso complementarios para versiones de vista previa de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!---HONumber=AcomDC_0302_2016-->
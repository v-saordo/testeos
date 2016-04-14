<properties 
   pageTitle="Asesor de índices de Base de datos SQL de Azure" 
   description="El Asesor de índices de Base de datos SQL recomienda índices nuevos para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="01/23/2016"
   ms.author="sstein"/>

# Asesor de índices de Base de datos SQL

El Asesor de índices de Base de datos SQL de Azure recomienda índices para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual. El servicio de la Base de datos SQL evalúa el rendimiento del índice mediante el análisis del historial de uso de la base de datos SQL. Se recomienda usar los índices que sean más adecuados para ejecutar la carga de trabajo habitual de su base de datos.

El Asesor de índices le ayuda a optimizar el rendimiento de la base de datos de la siguiente manera:

- Le recomienda cuáles son los índices que debería crear (las recomendaciones están disponibles solo para los índices que no son de clúster).
- Le recomienda cuáles son los índices que debería quitar (las recomendaciones para quitar índices se encuentran en vista previa y actualmente solo se pueden aplicar a los índices duplicados).
- Le permite optar por aplicar recomendaciones de índices automáticamente, sin intervención del usuario. (Las recomendaciones automatizadas requieren que el [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx) esté habilitado y se esté ejecutando.)
- Revierte de manera automática las recomendaciones que tienen un impacto negativo en el rendimiento. 


En este artículo se describe el Asesor de índices para servidores V12. Las recomendaciones de índices están disponibles para los servidores V11, pero debe ejecutar el script Transact-SQL (T-SQL) proporcionado para poder implementar la recomendación. El asesor no revertirá operaciones de índice en servidores V11 por lo que debe supervisar y revertir el impacto en el rendimiento según sea necesario.


### Permisos

Para ver y crear recomendaciones de índice, necesita los permisos de [control de acceso basado en rol](../active-directory/role-based-access-control-configure.md) correctos en Azure.

- Para ver las recomendaciones, se requieren los permisos de **Lector** y **Colaborador de Base de datos SQL**.
- Para ejecutar cualquier acción, crear o eliminar índices y cancelar la creación de índices, se requieren los permisos de **Propietario** y **Colaborador de Base de datos SQL**.


## Ver recomendaciones de índices

La página "Recomendaciones de índices" es donde puede ver los principales índices sugeridos según su impacto potencial para la mejora del rendimiento. También puede ver el estado de las últimas operaciones de índices. Seleccione una recomendación o estado para ver sus detalles.

Para ver recomendaciones de índices:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **EXAMINAR** > **Bases de datos SQL** y seleccione la base de datos.
5. Haga clic en **Toda la configuración** > **Asesor de índices** para ver las **recomendaciones de índices** disponibles para la base de datos seleccionada.

> [AZURE.NOTE] Para obtener recomendaciones de índices, una base de datos debe tener alrededor de una semana de uso y, dentro de esa semana, debe haber alguna actividad. También debe haber actividad coherente. El Asesor de índice puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares de actividad. Si no hay recomendaciones disponibles, la página **Recomendaciones de índices** debe proporcionar un mensaje explicando el motivo.

![Índices recomendados](./media/sql-database-index-advisor/recommendations.png)

Las recomendaciones se ordenan en las 4 siguientes categorías, según su impacto potencial en el rendimiento:

| Impacto | Descripción |
| :--- | :--- |
| Alto | Las recomendaciones de alto impacto debe tener el impacto más importante en el rendimiento. |
| Significativo | Las recomendaciones de impacto significativo deben mejorar considerablemente el rendimiento. |
| Moderado | Las recomendaciones de impacto moderado deben mejorar el rendimiento, pero no de manera significativa. |
| Bajo | Las recomendaciones de bajo impacto deben proporcionar un mejor rendimiento que el que ocurriría sin el índice, pero es posible que las mejoras no sean significativas. 
Utilice la etiqueta de impacto para determinar los mejores candidatos para la creación de índices nuevos.


### Quitar recomendaciones de índices de la lista

Si la lista de índices recomendados contiene índices que quiere quitar de la lista, puede descartar la recomendación:

1. Seleccione la recomendación en la lista de **Índices recomendados**.
2. Haga clic en **Descartar índice** en la hoja **Detalles del índice**.


Si quiere, puede volver a agregar índices descartados en la lista **Índices recomendados**:

1. En la hoja **Recomendaciones de índices**, haga clic en **Ver recomendaciones de índices descartadas**.
1. Seleccione un índice descartado en la lista para ver los detalles.
1. Opcionalmente, haga clic en **Deshacer descartar** para agregar el índice nuevamente a la lista principal de las **Recomendaciones de índices**.



## Aplicar recomendaciones de índices

Gracias al Asesor de índices, tiene el control completo sobre el modo en que se habilitan las recomendaciones de índices mediante cualquiera de las 3 opciones siguientes.

- Aplicar recomendaciones individuales una a una.
- Habilitar el Asesor de índices para que aplique automáticamente las recomendaciones de índices.
- Ejecutar manualmente el script T-SQL recomendado en la base de datos para implementar una recomendación.

Seleccione cualquier recomendación para ver sus detalles y, a continuación, haga clic en **Ver script** para revisar los detalles exactos del modo en que se creará la recomendación.

La base de datos permanece en línea mientras el asistente aplica la recomendación. Si usa el Asesor de índices, nunca se desconecta una base de datos.

### Aplicar una recomendación individual

Puede revisar y aceptar recomendaciones una a una.

1. En la hoja **Recomendaciones de índices**, haga clic en una recomendación.
2. En la hoja **Detalles de índice**, haga clic en **Aplicar**.

    ![Aplicar recomendaciones](./media/sql-database-index-advisor/apply.png)


### Habilitar la administración de índices automática

Puede establecer que el Asesor de índices implemente las recomendaciones de forma automática. A medida que las recomendaciones estén disponibles, estas se aplicarán de manera automática. Al igual que con todas las operaciones de índice que administra el servicio, si el impacto en el rendimiento es negativo, se revertirá la recomendación.

1. En la hoja **Recomendaciones de índices**, haga clic en **Configuración del asesor**.

    ![Configuración del asesor](./media/sql-database-index-advisor/settings.png)

2. Establezca si quiere permitir al asesor **Crear** o **Quitar** índices automáticamente:

    ![Índices recomendados](./media/sql-database-index-advisor/automation.png)




### Ejecutar manualmente el script T-SQL recomendado

Seleccione una recomendación y haga clic en **Ver script**. Ejecute este script en la base de datos para aplicar la recomendación manualmente.

*El servicio no supervisa ni valida los índices que se ejecutan de manera manual para conocer el impacto en el rendimiento*, por lo que se recomienda supervisar estos índices después de su creación para comprobar que proporcionen ganancias en el rendimiento y ajustarlos o eliminarlos, en caso de que sea necesario. Si desea conocer detalles sobre la creación de índices, consulte [CREAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelación de la creación de índices

Es posible cancelar los índices en estado **Pendiente**. No es posible cancelar los índices que se están creando (estado **En ejecución**).

1. Seleccione cualquier índice **Pendiente** en el área **Operaciones de índice** para abrir la hoja **Detalles del índice**.
2. Haga clic en **Cancelar** para anular el proceso de creación de índices.



## Supervisión de operaciones de índice

Puede que una recomendación no se aplique de manera inmediata. El portal proporciona detalles sobre el estado de las operaciones de índice. Cuando se administran los índices, un índice puede tener los siguientes estados:

| Estado | Descripción |
| :--- | :--- |
| Pending | Se recibió el comando para la creación de índice y el índice está programado para su creación. |
| Executing | El comando para la creación de índice está en ejecución y el índice se está creando. |
| Correcto | El índice se creó correctamente. |
| Con error | El índice no se creó. Puede tratarse de un problema transitorio, o posiblemente se produjo un cambio de esquema en la tabla y el script ya no es válido. |
| En reversión | El proceso de creación de índice se canceló o se consideró sin rendimiento, por lo que se revierte de manera automática. |

Haga clic en una recomendación en proceso de la lista para ver sus detalles:

![Índices recomendados](./media/sql-database-index-advisor/operations.png)



### Revertir un índice

Si usa el asesor para crear un índice (es decir, no ejecuta manualmente el script T-SQL), este revertirá dicho índice automáticamente en caso de que detecte que afecta de manera negativa en el rendimiento. Si simplemente quiere revertir una operación del Asesor de índices, por el motivo que sea, realice los siguientes pasos.


1. Seleccione un índice creado correctamente en la lista de **Operaciones de índice**.
2. Haga clic en **Revertir** en la hoja **Detalles del índice**, o bien haga clic en **Ver script** en un script DROP INDEX que pueda ejecutar.

![Índices recomendados](./media/sql-database-index-advisor/details.png)


## Supervisión del impacto en el rendimiento de las recomendaciones de índices

Una vez que se hayan implementado correctamente las recomendaciones, haga clic en **Información de consulta** en la hoja Detalles de índice para abrir [Query Performance Insight](sql-database-query-performance.md) y ver el impacto sobre el rendimiento de las consultas principales.

![Supervisar el impacto en el rendimiento](./media/sql-database-index-advisor/query-insights.png)


## Resumen

El Asesor de índices proporciona recomendaciones de índices y una experiencia automatizada para administrar índices en la Base de datos SQL. Al proporcionar scripts T-SQL, así como las opciones de administración de los índices de manera individual o completamente automática, el Asesor de índices resulta útil para optimizar los índices de las bases de datos y, en última instancia, para mejorar el rendimiento de las consultas.



## Pasos siguientes

Supervise las recomendaciones de índices y continúe aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. El Asesor de índices seguirá supervisando y recomendando índices que podrían mejorar el rendimiento de la base de datos.

<!---HONumber=AcomDC_0224_2016-->
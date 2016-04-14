<properties
	pageTitle="Solucionar problemas de copia de seguridad y restauración de Base de datos SQL de Azure"
	description="Aprenda a recuperar una base de datos de nube de errores e interrupciones mediante copias de seguridad y réplicas de Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="daleche"/>

# Restaurar una base de datos a un momento anterior en el tiempo, restaurar una base de datos eliminada o recuperarse de una interrupción del centro de datos

Base de datos SQL mantiene réplicas de la base de datos para permitir la recuperación tras interrupciones y errores del usuario. Las opciones disponibles dependen del nivel de servicio de la base de datos y de las opciones que elija. Vea la [Información general de continuidad empresarial](sql-database-business-continuity.md) para detalles y consideraciones de diseño.

##Para restaurar una base de datos a un momento dado anterior
1.	En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **Bases de datos SQL**.
2.	Seleccione la base de datos en la lista y luego haga clic en **Restaurar**.
3.	Escriba un nombre nuevo para la base de datos, elija la fecha y la hora desde la que restaurar, y luego haga clic en **Crear.**
4.	Realice ajustes de aplicación según sea necesario para hacer referencia a la nueva base de datos. Vea [Recuperación de una base de datos de un error de usuario](sql-database-user-error-recovery.md).

##Para restaurar una base de datos eliminada accidentalmente
1.	En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **Servidores SQL Server**.
2.	Seleccione el servidor que hospedaba la base de datos en la lista.
3.	En la hoja Servidor, desplácese hacia abajo y haga clic en **Bases de datos eliminadas**.
4.	Seleccione la base de datos que quiere restaurar y luego haga clic en **Crear**.
5.	Realice ajustes de aplicación según sea necesario para hacer referencia a la nueva base de datos. Vea [Recuperación de una base de datos de un error de usuario](sql-database-user-error-recovery.md).

##Para recuperarse de una interrupción del centro de datos regional
Con bases de datos Estándar y Premium, si configura secundarias replicadas geográficamente, puede recuperarlas con estas secundarias. Esto le ofrece la capacidad de restaurar una base de datos con un potencial menor de pérdida de datos. Vea [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md) para detalles.

Azure ofrece también copias de seguridad de cada base de datos en una región diferente (una copia de seguridad con redundancia geográfica). Puede crear una base de datos nueva a partir de estas copias de seguridad, lo que se denomina restauración geográfica, pero es probable que pierda datos si solo confía en este método.

**Para recuperar una base de datos mediante la restauración geográfica:**

- En el [Portal de Azure](https://azure.microsoft.com/), haga clic en **Nuevo**, en **Datos y almacenamiento**, haga clic en **Base de datos SQL** y luego seleccione **Copia de seguridad** como el origen de la base de datos. Vea [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md) para detalles.

<!---HONumber=AcomDC_0218_2016-->
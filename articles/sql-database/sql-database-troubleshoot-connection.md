<properties
	pageTitle="Solución del problema de que la base de datos del servidor no esté disponible para Base de datos SQL de Azure"
	description="Pasos para identificar y resolver errores de conexión para la Base de datos SQL de Azure."
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
	ms.date="02/12/2016"
	ms.author="daleche"/>

# Solución de problemas cuando la base de datos del servidor no está disponible y es necesario reintentar la conexión más tarde, así como otros errores de conexión
Un error que indica que la base de datos <dbname> del servidor <servername> no está disponible es el error de conexión transitorio más común para Base de datos SQL de Azure. Los errores de conexión transitorios se producen normalmente debido a un evento planeado (por ejemplo, una actualización de software) o a un evento no planeado (por ejemplo, el bloque de un proceso). Son por lo general de corta duración, desde unos segundos hasta un minuto como máximo. Si recibe un error diferente, evalúe el [mensaje de error](sql-database-develop-error-messages.md) para conseguir pistas sobre su causa, determine si el problema es transitorio o persistente y use las instrucciones de este tema.

## Pasos para resolver los problemas de conectividad transitorios
1.	Compruebe el [Panel de servicios de Microsoft Azure](https://azure.microsoft.com/status) para ver las interrupciones conocidas.
2.	Asegúrese de que su aplicación usa la lógica de reintento. Vea los [problemas de conectividad de](sql-database-connectivity-issues.md) y las [prácticas recomendadas y las directrices de diseño](sql-database-connect-central-recommendations.md) para estrategias de reintento generales. Luego vea [ejemplos de código](sql-database-develop-quick-start-client-code-samples.md) para información específica.
3.	Conforme una base de datos se acerca a sus límites de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
4.	Si los problemas de conectividad continúan, presente una solicitud de soporte técnico de Azure; para ello, seleccione **Obtener soporte** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options).

## Pasos para resolver los problemas de conectividad persistentes
Si la aplicación no se puede conectar en absoluto, suele deberse a la configuración del IP y del firewall. Esto puede incluir la reconfiguración de la red en el lado cliente (por ejemplo, un nuevo proxy o dirección IP). Los parámetros de conexión mal escritos, como la cadena de conexión, también son comunes.

1.	Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente.
2.	En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes.
3.	Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de problemas de conectividad](sql-database-connectivity-issues.md).
4.	Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.
5.	Si los problemas de conectividad continúan, presente una solicitud de soporte técnico de Azure; para ello, seleccione **Obtener soporte** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options).

<!---HONumber=AcomDC_0218_2016-->
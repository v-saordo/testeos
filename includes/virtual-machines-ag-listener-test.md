En este paso, probarás la escucha del grupo de disponibilidad mediante una aplicación de cliente que se ejecuta en la misma red.

Para la conectividad de cliente, ten en cuenta los siguientes requisitos:

- Las conexiones de cliente para el agente de escucha tienen que proceder de máquinas que residan en un servicio en la nube diferente al que hospeda las réplicas de disponibilidad AlwaysOn.

- Si las réplicas AlwaysOn están en subredes diferentes, los clientes tendrán que especificar «MultisubnetFailover=True» en la cadena de conexión. Esto se traduce en intentos de conexión en paralelo con las réplicas de las diferentes subredes. Ten en cuenta que este escenario incluye una implementación del grupo de disponibilidad AlwaysOn entre regiones.

Un ejemplo sería conectarse al agente de escucha desde una de las máquinas virtuales de la misma red virtual de Azure (pero no desde una que hospede una réplica). Una manera fácil de completar esta prueba consiste en intentar conectar el SSMS (SQL Server Management Studio) a la escucha del grupo de disponibilidad. Otro método sencillo es ejecutar [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) de la siguiente manera:

	sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [AZURE.NOTE]Si el valor de EndpointPort es 1433, no es necesario especificarlo en la llamada. La llamada anterior también asume que el equipo cliente está unido al mismo dominio y que el llamador tiene concedidos los permisos en la base de datos mediante la autenticación de Windows.

Al probar el agente de escucha, asegúrate de conmutar por error el grupo de disponibilidad para asegurarte de que los clientes puedan conectarse al agente de escucha a través de las conmutaciones por error.

<!-------HONumber=Oct15_HO3-->
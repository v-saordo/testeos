<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Obtenga la cadena de conexión del portal de Azure


Utilice el [portal de vista previa de Azure](https://portal.azure.com/) para obtener la cadena de conexión necesaria para que su programa cliente interactúe con Base de datos SQL de Azure:


1. Haga clic en **EXAMINAR** > **Bases de datos SQL**.

    ![Seleccionar SQL][1-select-sql]

2. Escriba el nombre de la base de datos en el cuadro de texto de filtro situado en la esquina superior izquierda de la hoja **Bases de datos SQL**.

    ![Selección de la base de datos][2-select-database]]

3. Haga clic en la fila correspondiente a la base de datos.

4. Cuando aparezca la hoja de su base de datos, para una mayor comodidad visual puede hacer clic en los controles estándar para minimizar y contraer las hojas que utilizó para examinar y filtrar de la base de datos.

5. En la hoja de la base de datos, haga clic en **Mostrar cadenas de conexión de la base de datos**.

6. Si piensa utilizar la biblioteca de conexiones de ADO.NET, copie la cadena etiquetada con **ADO.NET**.

	![Copie la cadena de conexión ADO.NET correspondiente a la base de datos][3-get-connection-string]

7. Pegue la información de la cadena de conexión en el código del programa cliente. Tendrá que sustituir {your\_password\_here} por su contraseña real.



Para más información, consulte [Cadenas de conexión y archivos de configuración](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-dotnet.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0128_2016-->
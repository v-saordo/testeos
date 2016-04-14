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

5. Tome nota del nombre de la **base de datos SQL** y el **nombre del servidor**. El nombre de usuario será yourusername@yourserver.

	![Obtención de detalles de la conexión][3-get-connection-details]

7.  Pegue los detalles de la conexión en el código del programa cliente. Tendrá que sustituir {your\_password\_here} por su contraseña real.


<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-details]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-details.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0128_2016-->
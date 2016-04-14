<properties
   pageTitle="Conexión de Excel a Hadoop con Hive ODBC Driver | Microsoft Azure"
   description="Vea cómo configurar y usar Microsoft Hive ODBC Driver para que Excel consulte datos en un clúster de HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="jgao"/>

#Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver

[AZURE.INCLUDE [Selector ODBC-JDBC](../../includes/hdinsight-selector-odbc-jdbc.md)]

La solución de datos de gran tamaño de Microsoft integra componentes de inteligencia empresarial (BI) de Microsoft con clústeres Apache Hadoop implementados por HDInsight de Azure. Un ejemplo de esta integración es la capacidad de conectar Excel al almacén de datos de Hive de un clúster Hadoop en HDInsight usando Microsoft Hive Open Database Connectivity (ODBC) Driver.

También es posible conectar desde Excel los datos asociados con un clúster de HDInsight y otros orígenes de datos, incluidos otros clústeres Hadoop (que no sean de HDInsight), con la utilización del complemento Microsoft Power Query para Excel. Para obtener información acerca de la instalación y el uso de Power Query, consulte [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query].

> [AZURE.NOTE] Aunque los pasos descritos en este artículo se pueden usar con un clúster de HDInsight basado en Linux o Windows, Windows es necesario para la estación de trabajo del cliente.

**Requisitos previos**:

Antes de empezar este artículo, debe tener lo siguiente:

- **Un clúster de HDInsight**. Para crear uno, consulte [Introducción a HDInsight de Azure][hdinsight-get-started].
- **Un equipo** con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.


##Instalación de Microsoft Hive ODBC Driver

Descargue e instale Microsoft Hive ODBC Driver desde el [Centro de descarga][hive-odbc-driver-download].

Este controlador puede instalarse en versiones de 32 o 64 bits de Windows 7, Windows 8, Windows Server 10, Windows Server 2008 R2 y Windows Server 2012 y permitirá la conexión con HDInsight de Azure (versión 1.6 y posterior) y el emulador de HDInsight de Azure (v.1.0.0.0 y posterior). Debe instalar la versión que coincida con la versión de la aplicación en que va a usar el controlador ODBC. Para este tutorial, el controlador se usará desde Office Excel.

##Creación de un origen de datos de Hive ODBC

En los siguientes pasos se explica cómo crear un origen de datos de Hive ODBC.

1. En Windows 8 o Windows 10, presione la tecla Windows para abrir la pantalla Inicio y luego escriba **orígenes de datos**.
2. Haga clic en **Configurar orígenes de datos ODBC (32 bits)** o **Configurar orígenes de datos ODBC (64 bits)**, en función de la versión de Office de que se trate. Si usa Windows 7, elija **Orígenes de datos ODBC (32 bits)** u **Orígenes de datos ODBC (64 bits)** en **Herramientas administrativas**. A continuación, se abrirá el cuadro de diálogo **Administrador de orígenes de datos ODBC**.

	![Administrador de orígenes de datos OBDC][img-hdi-simbahiveodbc-datasource-admin]

3. En la pestaña DNS de usuario, haga clic en **Agregar** para abrir el asistente **Crear nuevo origen de datos**.
4. Elija **Microsoft Hive ODBC Driver** y, a continuación, haga clic en **Finalizar**. Se abrirá el cuadro de diálogo **Microsoft Hive ODBC Driver DNS Setup**.

5. Escriba o seleccione los valores siguientes:

    Propiedad|Descripción
    ---|---
    Data Source Name|Asigne un nombre al origen de datos
    Host|Introduzca <HDInsightClusterName>.azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net
    Port|Use <strong>443</strong>. (Este puerto se ha cambiado de 563 a 443).
    Base de datos|Use el <strong>valor predeterminado</strong>
    Hive Server Type|Seleccione <strong>Hive Server 2</strong>
    Mechanism|Seleccione <strong>Azure HDInsight Service</strong>
    HTTP Path|Deje este parámetro en blanco.
    User Name|Escriba el nombre del usuario del clúster de HDInsight. Se trata del nombre de usuario creado durante el proceso de aprovisionamiento del clúster. Si ha usado la opción de creación rápida, el nombre de usuario predeterminado es <strong>admin</strong>.
    Password|Escriba la contraseña del usuario del clúster de HDInsight.
    </table>

    Hay algunos parámetros importantes que se deben tener en cuenta al hacer clic en **Opciones avanzadas**:

    Parámetro|Descripción
    ---|---
    Use Native Query|Cuando esta opción está seleccionada, el controlador ODBC NO tratará de convertir TSQL en HiveQL. Solo debe usarla si está totalmente seguro de que va a enviar instrucciones de HiveQL puras. Al conectarse a SQL Server o a la Base de datos SQL de Azure, debe dejar esta opción desactivada.
    Rows fetched per block|Al capturar un gran volumen de registros, es posible que sea necesario ajustar este parámetro para garantizar un rendimiento óptimo.
    Default string column length, Binary column length, Decimal column scale|La longitud y precisión del tipo de datos pueden afectar a la forma en que se devuelven los datos. Pueden dar lugar a que se devuelva información incorrecta debido a la pérdida de precisión o al truncamiento.


	![Opciones avanzadas][img-HiveOdbc-DataSource-AdvancedOptions]

6. Haga clic en **Probar** para probar el origen de datos. Cuando el origen de datos esté configurado correctamente, aparecerá el mensaje *Pruebas completadas correctamente*.
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba. Ahora el nuevo origen de datos debería aparecer en el **Administrador de orígenes de datos ODBC**.
8. Haga clic en **Aceptar** para salir del asistente.

##Importación de datos en Excel desde HDInsight

En los pasos siguientes se describe cómo importar datos desde una tabla de Hive a un libro de Excel mediante el origen de datos ODBC creado en los pasos anteriores.

1. Abra un libro de Excel nuevo o existente.
2. En la pestaña **Datos**, haga clic en **Desde otros orígenes de datos** y, a continuación, haga clic en **Desde el Asistente para la conexión de datos** para iniciar el **Asistente para la conexión de datos**.

	![Abrir el Asistente para la conexión de datos][img-hdi-simbahiveodbc.excel.dataconnection]

3. Seleccione **DSN ODBC** como el origen de datos y, a continuación, haga clic en **Siguiente**.
4. En los orígenes de datos ODBC, seleccione el nombre del origen de datos creado en el paso anterior y, a continuación, haga clic en **Siguiente**.
5. Vuelva a escribir la contraseña para el clúster en el asistente y, a continuación, haga clic en **Probar** para comprobar la configuración, si procede.
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba.
7. Haga clic en **Aceptar**. Espere a que se abra el cuadro de diálogo **Seleccionar base de datos y tabla**. Esta operación puede tardar unos segundos.
8. Seleccione la tabla que desea importar y, a continuación, haga clic en **Siguiente**. *hivesampletable* es una tabla de Hive de muestra integrada en los clústeres de HDInsight. Puede seleccionarla si no ha creado ninguna. Para obtener más información acerca de cómo ejecutar consultas de Hive y crear tablas de Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].
8. Haga clic en **Finalizar**
9. En el cuadro de diálogo **Importar datos**, puede cambiar o especificar la consulta. Para ello, haga clic en **Propiedades**. Esta operación puede tardar unos segundos.
10. Haga clic en la pestaña **Definición** y, a continuación, anexe **LIMIT 200** a la instrucción select de Hive en el cuadro de texto **Texto de comando**. La modificación limitará el conjunto de registros devueltos a 200.

	![Propiedades de conexión][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Propiedades de conexión.
12. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar datos**.  
13. Vuelva a escribir la contraseña y, a continuación, haga clic en **Aceptar**. La importación de los datos a Excel tarda algunos segundos.

##Pasos siguientes

En este artículo se proporciona información acerca de cómo usar Microsoft Hive ODBC Driver para recuperar datos del servicio HDInsight en Excel. De manera similar, puede recuperar datos del servicio HDInsight en la Base de datos SQL. Es posible cargar datos en un servicio HDInsight. Para obtener más información, consulte:

- [Análisis de la información de retraso de vuelos con HDInsight][hdinsight-analyze-flight-data]
- [Carga de datos en HDInsight][hdinsight-upload-data]
- [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png

<!---HONumber=AcomDC_0204_2016-->
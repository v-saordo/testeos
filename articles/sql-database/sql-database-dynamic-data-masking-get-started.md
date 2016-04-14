<properties
   pageTitle="Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)"
   description="Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/01/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure Classic Portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Información general

El enmascaramiento datos dinámicos de Base de datos SQL limita la exposición de información confidencial mediante el enmascaramiento a los usuarios sin privilegios. El enmascaramiento de datos dinámicos se admite con la versión V12 de la Base de datos SQL de Azure.

El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.

Por ejemplo, un representante de servicio de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos de su número de seguridad social o el número de tarjeta de crédito, pero esa es una información que no debería exponerse por completo al representante del servicio. Se puede definir una regla de enmascaramiento que enmascare todo excepto los cuatro últimos dígitos de un número de seguridad social o un número de tarjeta de crédito en el conjunto de resultados de cualquier consulta. Otro ejemplo, una máscara de datos apropiada se puede definir para proteger los datos de información de identificación personal, para que un desarrollador pueda consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones de cumplimiento.

## Conceptos básicos del enmascaramiento de datos dinámicos en Base de datos SQL

Para configurar una directiva de enmascaramiento de datos dinámicos en el Portal de Azure, se selecciona la operación de enmascaramiento de datos dinámicos en la hoja de configuración de Base de datos de SQL.


### Permisos de enmascaramiento de datos dinámicos

El enmascaramiento de datos dinámicos puede configurarse mediante el administrador de Base de datos de Azure, el administrador del servidor o los roles de autoridad de seguridad.

### Directiva de enmascaramiento de datos dinámicos

* **Usuarios de SQL excluidos del enmascaramiento**: conjunto de usuarios de SQL o identidades de AAD que obtendrán datos sin máscara en los resultados de consulta SQL. Tenga en cuenta que los usuarios con privilegios de administrador se excluirán siempre del enmascaramiento y verán los datos originales sin ninguna máscara.

* **Reglas de enmascaramiento**: un conjunto de reglas que definen los campos designados para el enmascaramiento y la función de enmascaramiento que se va a usar. Los campos designados se pueden definir mediante un nombre de esquema de base de datos, un nombre de tabla y un nombre de columna.

* **Funciones de enmascaramiento**: un conjunto de métodos que controlan la exposición de datos para diferentes escenarios.

| Función de enmascaramiento | Lógica de enmascaramiento |
|----------|---------------|
| **Valor predeterminado** |**Enmascaramiento completo según los tipos de datos de los campos designados**<br/><br/>• Use XXXX o menos X si el tamaño del campo es inferior a 4 caracteres para los tipos de datos de cadena (nchar, ntext, nvarchar).<br/>• Use un valor de cero para los tipos de datos numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para los tipos de datos de fecha y hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variante SQL, se usa el valor predeterminado del tipo actual.<br/>• Para XML, se usa el documento <masked/>.<br/>• Use un valor vacío para los tipos de datos especiales (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial).
| **Tarjeta de crédito** |**Método de enmascaramiento que expone los últimos cuatro dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de una tarjeta de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número de seguridad social** |**Método de enmascaramiento que expone los cuatro últimos dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de un número de seguridad social estadounidense.<br/><br/>XXX-XX-1234 |
| **Correo electrónico** | **Método de enmascaramiento que expone la primera letra y reemplaza el dominio con XXX.com** con una cadena constante como prefijo en el formato de una dirección de correo electrónico.<br/><br/>aXX@XXXX.com |
| **Número aleatorio** | **Método de enmascaramiento que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función de enmascaramiento será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método de enmascaramiento que expone el primero y el último carácter** y agrega una cadena de relleno personalizada en el medio. Si la cadena original es más corta que el prefijo y el sufijo expuestos, se usará únicamente la cadena de relleno. <br/>prefijo[relleno]sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### Campos recomendados para enmascarar

El motor de recomendaciones de DDM marca determinados campos de la base de datos como campos potencialmente confidenciales, que pueden ser buenos candidatos para el enmascaramiento. En la hoja Enmascaramiento de datos dinámicos del portal, verá las columnas recomendadas para la base de datos. Todo lo que debe hacer es hacer clic en **Agregar máscara** para una o más columnas y, luego, **Guardar** para aplicar una máscara para estos campos.

## Configurar el enmascaramiento de datos dinámicos para la base de datos mediante el Portal de Azure

1. Inicie el Portal de Azure en [https://portal.azure.com](https://portal.azure.com).

2. Desplácese hasta la hoja de configuración de la base de datos que incluye los datos confidenciales que desea enmascarar.

3. Haga clic en el icono **Enmascaramiento de datos dinámicos**, con lo que se iniciará la hoja de configuración del **Enmascaramiento de datos dinámicos**.

	* Como alternativa, puede desplazarse hacia abajo hasta la sección **Operaciones** y hacer clic en **Enmascaramiento de datos dinámicos**.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. En la hoja de **Enmascaramiento de datos dinámicos** puede ver algunas columnas de base de datos que el motor de recomendaciones ha marcado para enmascaramiento. Para aceptar las recomendaciones, solo tiene que hacer clic en **Agregar máscara** para una o varias columnas y se creará una máscara en función del tipo predeterminado para esta columna. Para cambiar la función de enmascaramiento, haga clic en la regla de enmascaramiento y edite formato de campo de enmascaramiento en el formato diferente que elija. Asegúrese de hacer clic en **Guardar** para guardar la configuración.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Para agregar una máscara para cualquier columna de la base de datos, en la parte superior de la hoja de configuración **Enmascaramiento de datos dinámicos**, haga clic en **Agregar máscara** para abrir la hoja de configuración **Agregar regla de enmascaramiento**.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Seleccione el **esquema**, la **tabla** y la **columna** para definir el campo designado para enmascararse.

7. Elija un **Formato de campo de enmascaramiento** en la lista de categorías de enmascaramiento de datos confidenciales.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>

8. Haga clic en **Guardar** en la hoja de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento dinámico.

9. Escriba los usuarios SQL o las identidades AAD que deben excluirse del enmascaramiento y tengan acceso a los datos confidenciales sin máscara. Esto debe ser una lista separada por puntos y coma de usuarios. Tenga en cuenta que los usuarios con privilegios de administrador siempre tienen acceso a los datos originales sin máscara.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

	>[AZURE.TIP] Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el usuario SQL o identidad AAD que la aplicación usa para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de usuarios con privilegio para minimizar la exposición de los datos confidenciales.

10. Haga clic en **Guardar** en la hoja de configuración de enmascaramiento de datos para guardar la directiva de enmascaramiento nueva o actualizada.

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante cmdlets de PowerShell

Consulte [Cmdlets de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante la API de REST

Consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0128_2016-->
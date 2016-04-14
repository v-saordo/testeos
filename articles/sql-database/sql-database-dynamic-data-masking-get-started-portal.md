<properties
   pageTitle="Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de Azure clásico)"
   description="Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure clásico"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="jeffreyg"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/01/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de Azure clásico)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure Portal](sql-database-dynamic-data-masking-get-started.md)

## Información general

El enmascaramiento datos dinámicos de Base de datos SQL limita la exposición de información confidencial mediante el enmascaramiento a los usuarios sin privilegios. El enmascaramiento de datos dinámicos se admite con la versión V12 de la Base de datos SQL de Azure.

El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.

Por ejemplo, un representante de servicio de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos de su número de seguridad social o el número de tarjeta de crédito, pero esa es una información que no debería exponerse por completo al representante del servicio. Se puede definir una regla de enmascaramiento que enmascare todo excepto los cuatro últimos dígitos de un número de seguridad social o un número de tarjeta de crédito en el conjunto de resultados de cualquier consulta. Otro ejemplo, una máscara de datos apropiada se puede definir para proteger los datos de información de identificación personal, para que un desarrollador pueda consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones de cumplimiento.

## Conceptos básicos del enmascaramiento de datos dinámicos en Base de datos SQL

Configuración de la directiva de enmascaramiento de datos dinámicos en el Portal de Azure clásico en la pestaña Auditoría y seguridad para la base de datos.


> [AZURE.NOTE] Para configurar el enmascaramiento de datos dinámicos en el Portal de Azure, consulte [Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md).


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
| **Número aleatorio** | **Método de enmascaramiento que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función de enmascaramiento será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método de enmascaramiento que expone el primero y el último carácter** y agrega una cadena de relleno personalizada en el medio. Si la cadena original es más corta que el prefijo y el sufijo expuestos, se usará únicamente la cadena de relleno.<br/>prefijo[relleno]sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante el Portal de Azure clásico

1. Inicie el Portal de Azure clásico en [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Haga clic en la base de datos que desea enmascarar y luego en la pestaña **AUDITORÍA Y SEGURIDAD**.

3. En **enmascaramiento de datos dinámicos**, haga clic en **HABILITADO** para habilitar la característica de enmascaramiento de datos dinámicos.

4. Escriba los usuarios SQL o las identidades AAD que deben excluirse del enmascaramiento y tengan acceso a los datos confidenciales sin máscara. Esto debe ser una lista separada por puntos y coma de usuarios. Tenga en cuenta que los usuarios con privilegios de administrador siempre tienen acceso a los datos originales sin máscara.

	>[AZURE.TIP] Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el usuario SQL o identidad AAD que la aplicación usa para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de usuarios con privilegio para minimizar la exposición de los datos confidenciales.

	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. En la parte inferior de la página en la barra de menús, haga clic en **Agregar MÁSCARA** para abrir la ventana de configuración de la regla de enmascaramiento.

6. Seleccione el **esquema**, la **tabla** y la **columna** en las listas desplegables para definir los campos designados para enmascararse.

7. Elija una **FUNCIÓN DE ENMASCARAMIENTO** en la lista de categorías de enmascaramiento de datos confidenciales.

	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Haga clic en **Aceptar** en la ventana de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento de datos dinámicos.

9. Haga clic en **GUARDAR** para guardar la directiva de enmascaramiento nueva o actualizada.


## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante cmdlets de PowerShell

Consulte [Cmdlets de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante la API de REST

Consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0128_2016-->
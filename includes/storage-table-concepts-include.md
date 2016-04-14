## ¿Qué es el servicio Tabla?

El servicio de almacenamiento de tablas de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Entre los usos frecuentes del servicio Tabla se encuentran los siguientes:

-   Almacenamiento de TB de datos estructurados capaces de ofrecer servicio a aplicaciones de escalado web
-   Almacenamiento de conjuntos de datos que no requieren uniones complejas, claves externas o procedimientos almacenados y que pueden desnormalizarse para obtener un acceso rápido
-   Consulta rápida de datos mediante un índice agrupado
-   Acceso a datos mediante el protocolo OData y las consultas LINQ con la bibliotecas .NET del servicio de datos de WCF

Puede usar el servicio Tabla para almacenar grandes conjuntos de datos estructurados no relacionales y realizar consultas sobre ellos, y las tablas se escalarán a medida que aumente la demanda.

## Conceptos del servicio Tabla

El servicio Tabla contiene los siguientes componentes:

![Table1][Table1]

-   **Formato de dirección URL:** el código trata las tablas en una cuenta con este formato de dirección: http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Puede desviar las tablas de Azure directamente mediante esta dirección con el protocolo OData. Para obtener más información, consulte [OData.org][].

-   **Cuenta de almacenamiento:** todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento.

-   **Tabla**: una tabla es una colección de entidades. Las tablas no exigen un esquema sobre entidades, lo que significa que una única tabla puede contener entidades que dispongan de diferentes conjuntos de propiedades. El número de tablas que una cuenta de almacenamiento puede contener se encuentra restringido solo por el límite de capacidad de la cuenta de almacenamiento.

-   **Entidad**: una entidad es un conjunto de propiedades, similar a una fila de base de datos. Una entidad puede tener un tamaño de hasta 1 MB.

-   **Propiedades**: una propiedad es un par nombre-valor. Cada entidad puede incluir hasta 252 propiedades para almacenar datos. Cada entidad dispone también de tres propiedades del sistema que especifican una clave de partición, una clave de fila y una marca de tiempo. Pueden realizarse consultas en las entidades con la misma partición de manera más rápida e insertarse o actualizarse en operaciones atómicas. Una clave de fila de la entidad es el identificador exclusivo en una partición.


  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/

<!----HONumber=Oct15_HO3-->
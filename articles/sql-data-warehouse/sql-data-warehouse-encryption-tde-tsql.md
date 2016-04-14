<properties 
   pageTitle="Introducción al cifrado de datos transparente (TDE) del Almacenamiento de datos SQL TSQL | Microsoft Azure" 
   description="Introducción al cifrado de datos transparente (TDE) del Almacenamiento de datos SQL TSQL" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/07/2016" 
   ms.author="mausher;barbkess;sonyama"/>
 
# Introducción al cifrado de datos transparente (TDE)
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

El Cifrado de datos transparente (TDE) del Almacenamiento de datos SQL facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin requerir cambios en la aplicación.

TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos. En Base de datos SQL la clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Base de datos SQL. Microsoft alterna automáticamente estos certificados al menos cada 90 días. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)].

##Habilitar el cifrado

Para habilitar TDE para un Almacenamiento de datos SQL, siga estos pasos:

1. Conéctese a la base de datos *maestra* en el servidor que hospeda la base de datos mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Deshabilitar el cifrado

Para deshabilitar TDE para un Almacenamiento de datos SQL, siga estos pasos:

1. Conéctese a la base de datos *maestra* mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Comprobación del cifrado

Para comprobar el estado del cifrado para un Almacenamiento de datos SQL, siga estos pasos:

1. Conéctese a la base de datos *maestra* o de instancia mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Un resultado de ```1``` indica una base de datos cifrada, ```0``` indica una base de datos no cifrada.

 
<!--Anchors-->
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0114_2016-->
<properties
	pageTitle="Solucionar problemas de acceso y de permisos de bases de datos SQL de Azure"
	description="Pasos rápidos para solucionar problemas comunes de permisos, acceso, usuarios e inicio de sesión"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="v-shysun"/>

#Solucionar comunes problemas de acceso y de permisos de bases de datos SQL de Azure
Use este tema para pasos rápidos, para conceder y quitar el acceso a una Base de datos SQL de Azure. Para información más completa, vea:

- [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)
- [Protección de bases de datos SQL](sql-database-security.md)
- [Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

##Para cambiar la contraseña administrativa para un servidor lógico
- En el [Portal de Azure](https://portal.azure.com) haga clic en **Servidores SQL Server**, seleccione el servidor en la lista y luego haga clic en **Restablecer contraseña**.
##Para asegurarse de que solo se permiten las direcciones IP autorizada para tener acceso al servidor
- Vea [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).

##Para crear usuarios de bases de datos independientes en la base de datos de usuario
- Use la instrucción [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) y vea Base de datos independiente [Usuarios - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Para autenticar usuarios de bases de datos independientes con Azure Active Directory
- Vea [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).

## Para crear inicios de sesión adicionales para usuarios con privilegios elevados en la base de datos maestra virtual
-Use la instrucción [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) y vea la sección Administración de inicios de sesión de [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md) para más información.

<!---HONumber=AcomDC_0114_2016-->
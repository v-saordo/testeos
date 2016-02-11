<properties
	pageTitle="Inicios de sesión tras varios errores"
	description="Un informe que indica los usuarios que han iniciado sesión correctamente después de varios intentos del inicio de sesión consecutivos."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Inicios de sesión tras varios errores
Este informe indica los usuarios que han iniciado sesión correctamente después de varios intentos del inicio de sesión consecutivos. Las posibles causas son: <ul><li>El usuario había olvidado su contraseña</li><li>El usuario es víctima de una ataque por fuerza bruta para averiguar la contraseña con éxito</li></ul><p>Los resultados de este informe mostrarán el número de intentos de inicio de sesión consecutivos fallidos realizados antes del inicio de sesión correcto y una marca de tiempo asociada al primer inicio de sesión correcto.</p><p><b>Configuración de informes</b>: puede configurar el número mínimo de intentos de inicio de sesión fallidos consecutivos que se deben producir antes de que se puedan mostrar en el informe. Al realizar cambios en esta configuración, es importante tener en cuenta que estos cambios no se aplicarán a cualquier inicio de sesión erróneo existente que se muestra actualmente en el informe existente. Sin embargo, se aplicarán a todos los inicios de sesión futuros. Los cambios realizados en este informe solo los pueden llevar a cabo los administradores con licencia.


![Inicios de sesión tras varios errores](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=Oct15_HO3-->
<properties
	pageTitle="Actividad de inicio de sesión irregular"
	description="Informe que incluye inicios de sesión que nuestros algoritmos de aprendizaje automático identificaron como anómalos."
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

# Actividad de inicio de sesión irregular

Inicios de sesión irregulares son aquellos que han sido identificados por los algoritmos de aprendizaje automático, de acuerdo con una condición de "viaje imposible" combinado con una ubicación y un dispositivo inicio de sesión anómalo. Esto puede indicar que un hacker ha inicio sesión con esta cuenta. Se enviará una notificación por correo electrónico a los administradores globales si encontramos 10 o más eventos de inicio de sesión anómalos dentro de un intervalo de 30 días o menos. Asegúrese de incluir aad-alerts-noreply@mail.windowsazure.com en la lista de remitentes seguros.

<!---HONumber=Oct15_HO3-->
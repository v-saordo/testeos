<properties
	pageTitle="Inicios de sesión desde dispositivos posiblemente infectados"
	description="Un informe que incluye intentos de inicio de sesión que se han ejecutado desde dispositivos en los que se puede estar ejecutando algún malware (software malintencionado)."
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


# Inicios de sesión desde dispositivos posiblemente infectados
Este informe intenta identificar los dispositivos de los usuarios que han sido infectados y que forman ahora parte de una botnet (también conocido como ejército zombie). Correlacionamos las direcciones IP de inicios de sesión de usuarios con las direcciones IP que sabemos que están en contacto con servidores de botnet.

Recomendación: este informe marca direcciones IP y no dispositivos de usuario. Se recomienda ponerse en contacto con el usuario y examinar todos sus dispositivos para estar seguro. También es posible que el dispositivo personal del usuario esté infectado o que alguien que no sea el usuario, que estaba usando la misma dirección IP que el usuario, tenga un dispositivo infectado.

Para obtener más información acerca de cómo tratar infecciones de malware, consulte el [Centro de protección contra malware](http://go.microsoft.com/fwlink/?linkid=335773).

![Inicios de sesión desde dispositivos posiblemente infectados](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)

<!---HONumber=Oct15_HO3-->
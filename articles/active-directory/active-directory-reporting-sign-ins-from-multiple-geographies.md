<properties
	pageTitle="Inicios de sesión desde varias ubicaciones geográficas"
	description="Informe que señala usuarios en los que dos inicios de sesión parecían originarse en distintas regiones y, por el tiempo transcurrido entre inicios de sesión, resultaba imposible que el usuario viajase entre dichas regiones."
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

# Inicios de sesión desde varias ubicaciones geográficas
<p>Este informe incluye inicios de sesión correctos de un usuario en los que parece que dos inicios de sesión se originaron desde distintas regiones y que, según el tiempo pasado entre los inicios de sesión, parece imposible que el usuario haya viajado entre dichas regiones. Las posibles causas son:</p><ul><li>El usuario comparte su contraseña con otros.</li><li>El usuario usa un Escritorio remoto para iniciar un explorador web para iniciar sesión.</li><li>Un hacker ha iniciado sesión en la cuenta de un usuario desde otro país.</li><li>El usuario usa una VPN o un proxy.</li><li>El usuario ha iniciado sesión desde varios dispositivos a la vez, como un PC de escritorio y un teléfono móvil, y la dirección IP del teléfono móvil es inusual.</li></ul><p>Los resultados de este informe mostrarán los eventos de inicio de sesión correcto, así como el tiempo entre los inicios de sesión, las regiones desde donde parece que se originaron los inicios de sesión y el tiempo estimado de viaje entre las regiones.</p><p>El tiempo de viaje mostrado solo es una estimación y puede ser distinto del tiempo de viaje real entre las ubicaciones.</p>


![Inicios de sesión desde varias ubicaciones geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=Oct15_HO3-->
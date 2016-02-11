<properties
	pageTitle="Aprovisionamiento de aplicaciones basada en atributos con filtros de ámbito | Microsoft Azure"
	description="Obtenga información sobre cómo usar los filtros de ámbito para evitar el aprovisionamiento real de los objetos de las aplicaciones que admiten el aprovisionamiento automático de usuarios, en caso de que un objeto no satisfaga los requisitos empresariales."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="markusvi"/>


# Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito

El objetivo de esta sección es explicar cómo usar filtros de ámbito para definir reglas basadas en atributos que determinarán qué usuarios se aprovisionarán en la aplicación.





## Cláusulas y grupos de ámbitos


![Filtro de ámbito][1]




Los filtros de ámbito se definen mediante uno o varios **grupos de ámbitos**, y cada uno de ellos contiene una o varias **cláusulas**. Para ver las cláusulas de un grupo de ámbitos concreto, haga clic en la flecha situada a la izquierda del nombre del grupo para expandirlo.

Una **cláusula** determina qué usuarios pueden pasar a través del filtro de ámbito mediante la evaluación de los atributos de cada usuario. Por ejemplo, podría tener una cláusula que requiere que el atributo "state" del usuario sea igual a Nueva York, lo que significa que sólo se aprovisionará a los usuarios de Nueva York en la aplicación.

![Nombre del grupo de ámbito][2]



Cada **grupo de ámbitos** comienza con una **cláusula** obligatoria, como se muestra en la captura de pantalla anterior. Esta cláusula sólo indica que primero es necesario asignar al usuario a la aplicación antes de que los filtros de ámbito lo evalúen. Esta cláusula no puede eliminarse ni modificarse.

Puede agregar cláusulas nuevas o grupos de ámbitos nuevos; para ello, haga clic en el botón correspondiente. Puede asignar un nombre a cada grupo de ámbitos; para ello, edite la propiedad **Nombre del grupo de ámbitos**.





## Evaluación de los filtros de ámbito

Durante el aprovisionamiento, probamos a cada usuario asignado con los filtros de ámbito para determinar si es posible dar acceso a un usuario a la aplicación. Puede considerar cada cláusula como una prueba que se debe superar para evitar que el filtro rechace a un usuario.

Si ha definido varios grupos de ámbitos, cada usuario debe pasar al menos uno de ellos para poder tener acceso a la aplicación. Dentro de cada grupo de ámbitos, sin embargo, el usuario debe pasar cada cláusula para pasar ese grupo de ámbitos específico.

En otras palabras, puede considerar los grupos de ámbitos como agrupaciones mediante el operador OR, y puede considerar las cláusulas contenidas en ellos como agrupaciones mediante el operador AND. Por ejemplo, observe el siguiente filtro de ámbito:


![Nombre del grupo de ámbito][2]


Según este filtro de ámbito, los usuarios deben cumplir los siguientes criterios, a fin de que se les pueda aprovisionar:

1. Se les debe haber asignado a la aplicación.

2. Deben trabajar en el departamento de ingeniería.

3. Deben trabajar en San Francisco o Canadá.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

<!---HONumber=AcomDC_0107_2016-->
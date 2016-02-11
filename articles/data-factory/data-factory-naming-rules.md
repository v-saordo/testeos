<properties 
	pageTitle="Factoría de datos: reglas de nomenclatura | Microsoft Azure" 
	description="Describe las reglas de nomenclatura para las entidades de Factoría de datos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: reglas de nomenclatura 
La tabla siguiente proporciona las reglas de nomenclatura para los artefactos de Factoría de datos.



Nombre | Exclusividad del nombre | Comprobaciones de validación
:--- | :-------------- | :----------------
Factoría de datos | Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas, p. ej., MyDF y mydf hacen referencia a la misma factoría de datos. |<ul><li>Cada factoría de datos está asociada a exactamente una suscripción de Azure.</li><li>Los nombres de objetos deben comenzar con una letra o un número y solo pueden contener letras, números y el carácter de guión (-).</li><li>Cada carácter de guión (-) debe estar precedido y seguido inmediatamente por una letra o un número; no se permiten guiones consecutivos en los nombres de contenedor.</li><li>El nombre puede tener entre 3 y 63 caracteres.</li></ul>
Servicios, tablas o canalizaciones vinculados | Único en una factoría de datos. Los nombres no distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres en un nombre de tabla: 260.</li><li>Los nombres de objetos deben comenzar con un número, una letra o un carácter de subrayado (_).</li><li>No se permiten los siguientes caracteres: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\"</li></ul>
Grupo de recursos | Único en Microsoft Azure. Los no nombres distinguen mayúsculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>El nombre puede contener letras, dígitos y los siguientes caracteres: "-","_",","y".".</li></ul>

<!---HONumber=AcomDC_0128_2016-->
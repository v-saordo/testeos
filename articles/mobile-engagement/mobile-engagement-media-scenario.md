<properties 
	pageTitle="Implementación de Azure Mobile Engagement para una aplicación de medios"
	description="Escenario de aplicación de medios para implementar Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="piyushjo"/>

#Implementación de Mobile Engagement con una aplicación de medios

## Información general

John es un encargado de proyectos móviles para una gran empresa de medios. Acaba de lanzar una nueva aplicación con un número muy elevado de descargas. Ha cumplido sus objetivos en lo que respecta a las descargas, pero la rentabilidad de la inversión (ROI) por usuario no los cumple.

John ya ha identificado por qué su ROI es demasiado baja. Los usuarios suelen dejan de usar la aplicación después de solo 2 semanas y la mayoría no regresa nunca. Quiere aumentar la retención de su aplicación.

Después de algunas pruebas iniciales, ha descubierto que, cuando interacciona con sus usuarios mediante notificaciones push, tienden a seguir utilizando la aplicación. Además, los usuarios que estaban inactivos a menudo volverán a la aplicación en función de las notificaciones que les envíe. John decide invertir en algún tipo de programa de compromiso para su aplicación que use funciones avanzadas para dirigir las notificaciones push a destinatarios específicos.

John acaba de leer [Azure Mobile Engagement: Guía de introducción con procedimientos recomendados](mobile-engagement-getting-started-best-practices.md) y ha decidido implementar las recomendaciones de la guía.

##Objetivos y KPI

Las partes interesadas clave de la aplicación de John se reúnen. Se generan ingresos a partir de anuncios mientras los usuarios consumen sus medios. Si aumenta el contenido consumido por usuario, John aumenta sus ingresos. Todos están de acuerdo en un objetivo principal: aumentar las ventas a partir de anuncios en un 25%. Crean indicadores clave de rendimiento (KPI) de negocio para medir y promover este objetivo.

* Número de anuncios pulsados por usuario
* Cantidad de páginas de artículos visitadas (por usuario/por sesión/por semana/por mes…)
* Cuáles son las categorías favoritas

Tras la reunión de John con las personas interesadas clave, ha definido sus KPI de negocio. Sigue la parte 1 de [Azure Mobile Engagement: Guía de introducción con procedimientos recomendados](mobile-engagement-getting-started-best-practices.md).

Después, crea los siguientes KPI de compromiso para asegurarse de que se alcancen los objetivos:

* Supervisar la retención en los siguientes intervalos: a diario, semanalmente, quincenalmente y mensualmente.
* Recuentos de usuarios activos
* La clasificación de la aplicación en las tiendas de aplicaciones

Según las recomendaciones del equipo de TI, se han agregado los siguientes KPI técnicos para responder a las preguntas siguientes:

* ¿Cuál es la ruta de acceso de los usuarios (qué página visitan, cuánto tiempo pasan los usuarios en ella)?
* ¿Número de bloqueos o errores encontrados por sesión?
* ¿Qué versiones de sistema operativo ejecutan mis usuarios?
* ¿Cuál es el tamaño medio de pantalla de mis usuarios?
* ¿Qué tipo de conexiones a Internet tienen mis usuarios?

Para cada KPI, clasifica los datos necesarios y los registra en la ubicación adecuada de su cuaderno de estrategias.

## Programa de compromiso e integración

Ahora que John ha terminado de definir sus KPI, comienza la fase de estrategia de compromiso y define cuatro programas de compromiso y sus objetivos: ![][1]

Después, John profundiza aún más y detalla las notificaciones push para cada programa. La definición de la notificación push consta de cinco elementos:

1. Objetivo: cuál es el objetivo de la notificación
2. Cómo se alcanzará el objetivo
3. Destinatarios: quiénes recibirán la notificación
4. Contenido: cuál es el texto y el formato de la notificación (en la aplicación/fuera de la aplicación)
5. Cuándo: cuál es el mejor momento para enviar esta notificación push

	![][2]

Para más información, consulte los [cuadernos de estrategias](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Según la parte 2 de las notas del producto, John usa la sección sobre destinatarios para definir los datos que tiene que recopilar y escribe su plan de etiquetas junto con el equipo de TI para implementar la solución. Después de una semana de implementación y pruebas de aceptación del usuario, John finalmente lanza sus programas.

##Resultados de los programas

Cuatro meses después, John evalúa el rendimiento de los programas. El Programa de bienvenida y el Programa semanal están cumpliendo sus objetivos. Ha disminuido el número de usuarios con solo una sesión, se usan más características de la aplicación y se ha duplicado el número de conexiones por semana.

El **Programa de inactivos** está ayudando a John a entender las tendencias de los usuarios. Parece que el 15% de los usuarios inactivos vuelven a la aplicación. Sin embargo, la mayoría no permanecen activos más de 1 mes. John prevé una posible optimización de esta secuencia con notificaciones adicionales y mayor selección de contenido.

El **Programa de descubrimiento** no funciona bien. Aumenta las ventas cruzadas, pero no lo bastante como para alcanzar sus objetivos. John identifica que no dispone de los suficientes datos para dirigirse a los destinatarios relevantes y sugerir contenido adecuado. Abandona este programa y se centra en enviar "notificaciones push editoriales" con Azure Mobile Engagement. Sus periodistas ya cuentan con una solución CMS para enviar notificaciones push y no quieren cambiar.

John decide usar la API de cobertura, una API de REST de HTTP que permite administrar campañas de cobertura sin tener que usar la interfaz web de AZME. Con este enfoque, John puede recopilar los datos que necesita y permitir que sus periodistas sigan usando la solución CMS.

Para asegurarse de que esta característica funciona correctamente, John pide al equipo de TI que estén atentos a los siguientes puntos:

1. **Sistemas operativos**: cada uno tiene sus propias reglas para administrar las notificaciones push, por lo que John decide enumerar todos los casos y comprueba si las API los controlan. Por ejemplo: el sistema push de Android permite una imagen grande, al contrario que iOS.

2. **Período de tiempo**: John quiere una API que establezca el período de tiempo y el final de las campañas. Quiere impedir que los usuarios reciban un molesto bombardeo de notificaciones.

3. **Categorías**: el equipo de marketing prepara la plantilla para cada tipo de alerta. John pide al equipo de TI que establezcan categorías dentro de la API.

Tras varias pruebas, John está satisfecho. Gracias a esta API, los periodistas pueden seguir enviando notificaciones push con su CMS y Azure Mobile Engagement recopila todos los datos de comportamiento para ellas.

Después de estos cuatro primeros meses, los resultados reflejan un buen rendimiento general e inspiran confianza en John y la junta, la ROI por usuario ha aumentado en un 15% y las ventas móviles representan un 17,5% del total, un aumento del 7,5% en solo cuatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_0302_2016-->
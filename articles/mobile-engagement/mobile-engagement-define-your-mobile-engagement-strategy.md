<properties
	pageTitle="Definición de la estrategia de Mobile Engagement | Microsoft Azure"
	description="Aprenda cómo integrar y optimizar Mobile Engagement con análisis y notificaciones de inserción."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/01/2016"
	ms.author="piyushjo" />

# Definición de la estrategia de Mobile Engagement

*Ha editado su aplicación por un motivo: para que la utilicen los usuarios.*

Estamos convencidos de que dedicó mucho esfuerzo para crear una aplicación extraordinaria que apasionará a los usuarios. Probablemente ha invertido una cantidad considerable del presupuesto de marketing para conseguir usuarios. Pero después del emocionante pico inicial de usuarios, puede que observe que poco a poco dejaron de usar la aplicación. *Ahí es donde entra Azure Mobile Engagement*, que consiste en conservar a los usuarios y le permite mejorar la aplicación gradualmente mediante pruebas y aprender.

Nuestro enfoque para mejorar la retención y el uso se basa en atraer a los usuarios a través de notificaciones de inserción y mensajes en la aplicación, sobre todo, con mensajes y comunicación hechos a la medida de cada uno, en función de su comportamiento en la aplicación. Nuestro objetivo es permitirle comunicarse con la audiencia adecuada en el momento y el lugar adecuados.

Para ello, tendrá que empezar por *entender a los usuarios*, crear grupos según sus acciones o sus características (lo que denominados segmentos) y luego crear comunicaciones relevantes para cada segmento.

## Mobile Engagement cumple sus objetivos

*Hemos mencionado la retención y el uso, pero ¿para qué?*

Para crear la estrategia de Mobile Engagement primero hay que ver los objetivos y los indicadores clave del rendimiento (KPI) de la aplicación.

Empezar con la definición de estos objetivos y KPI que le ayudarán a definir casos de uso de participación de los usuarios con el enfoque adecuado.

Los casos de uso son una lista de las campañas que le gustaría hacer para comunicarse con los usuarios, que van desde un simple mensaje de bienvenida a la notificación de utilidad avanzada que desencadena su sistema TI. Un caso de uso bien construido debe incluir al menos los tres interrogantes *qué, quién y cuándo*:

1. Una designación muy breve (p. ej., "Campaña de bienvenida")
2. **Qué**: un mensaje de ejemplo (p. ej., "¡Es un placer tenerle a bordo!". No se olvide de iniciar sesión para obtener el primer mes gratuito"). Este mensaje no es definitivo, podrá cambiarlo en cualquier momento, pero normalmente es útil para comenzar a pensar en lo que queremos comunicar.
3. **Quién**: el segmento que recibirá este mensaje (p. ej., "Todos los usuarios que iniciaron la aplicación por primera vez hace 3 días han visitado la página de inicio de sesión pero no han iniciado sesión").
	- Sí, con Azure Mobile Engagement es así de fácil :)
	- En este caso también, este mensaje no tiene por qué ser definitivo, ya que puede definir los segmentos en cualquier momento, pero al principio es importante definir los criterios de segmentación para asegurarse de que recopila los datos adecuados.
4. **Cuándo**: el plazo de la campaña. Puede ser en una fecha concreta o después de una acción específica, en función de un desencadenador. Mobile Engagement ofrece una cantidad de posibilidades considerable para establecer el plazo correcto de su comunicación.

Una vez definidos los casos de uso y el segmento, ofrece instrucciones para definir los datos que deben recopilarse en una aplicación. Este es el rol de un *"plan de etiqueta"*. Un plan de etiqueta garantiza que la recopilación de datos se especifica a los desarrolladores. Por lo tanto, los desarrolladores pueden incrustar Mobile Engagement con la configuración adecuada para que trabaje en sus campañas con los datos correctos. También será muy importante ejecutar pruebas para garantizar que la integración es correcta y recopila lo que necesita.

En función de la integración, una vez publicadas las aplicaciones, usted, como vendedor, podrá ver sus análisis en tiempo real, segmentar la audiencia y, a continuación, comenzar a enviar la notificación de inserción inteligente orientada para atraer a los usuarios finales en la aplicación o fuera de ella.

### Casos de uso para comenzar
1. Estrategia de bienvenida: cree varias campañas de notificación de inserción en función del comportamiento del usuario final en el momento del lanzamiento de la aplicación para volver a conseguir la participación de los usuarios el D+2/5/10/15 después de la primera sesión y aumentar la retención de la primera ejecución.
2. Promocione nuevo contenido (características, artículos y vídeos, productos, etc.) en función del comportamiento del usuario final para enviar la información únicamente a los usuarios finales con más probabilidades de participar.
3. Valore la aplicación: céntrese en menos del 1 % de la base de usuarios con más probabilidad de asignar a la aplicación una valoración de 5 estrellas en la tienda.
4. Aumente las suscripciones: promocione contenido valioso a los usuarios finales que aún no hayan visto para aumentar la suscripción.
5. Tutorial: se acabaron los tutoriales obligatorios para todos los usuarios. ¿Por qué no crear tutoriales extraordinarios en la aplicación y activarlos a través de mensajes en la aplicación solo si el usuario parece que no está acostumbrado a usar una característica o tiene problemas con ella?

## ¿Por qué necesita análisis para atraer la participación?

Como habrá observado en este punto, no basta con realizar una notificación de inserción de difusión. El concepto básico de Mobile Engagement es ayudar a los vendedores y los desarrolladores a relacionarse con el usuario final adecuado a la hora y el lugar adecuados. Para conocer los tres conceptos principales, es esencial recopilar análisis de la aplicación y usarlos para segmentar la audiencia. Esto es incluso más eficaz cuando el comportamiento de los segmentos complementa datos de sus otras bases de datos o de CRM, o bien de un canal cruzado. Mobile Engagement le permite recuperar datos desde cualquier lugar y los usa para dirigirse a la audiencia adecuada.

Para ser lo más contextual posible al atraer la participación de la audiencia, es fundamental conocer el comportamiento de los usuarios finales para conocer su estado en tiempo real. Con la recopilación de datos, los vendedores pueden centrarse realmente en lo importante para reproducir casos de uso y lograr los objetivos de la estrategia de Mobile Engagement. El logro de los objetivos fijados anteriormente también explica el motivo por el que la práctica recomendada no es recopilar cualquier cosa sino solamente aquello que le permita centrarse en lo que quiere saber y en los casos de uso. Esta es una buena manera de empezar, probar y aprender a usar la solución y a abordar la notificación de inserción inteligente y aumentar la retención de una aplicación a tal nivel que se convierta en una historia de éxito.

>[AZURE.NOTE] Recuerde: ¡demasiados datos suponen la muerte de los datos!

### Casos de uso y prácticas recomendadas

En las secciones siguientes trataremos brevemente algunos casos de uso clave que hemos experimentado con nuestros clientes para que pueda comenzar.

#### Multimedia

Recopile el tipo de contenido que consume el usuario final y segmente la audiencia en función de ese comportamiento para dirigir un tipo de contenido específico solo a un público con más probabilidad de consumirlo. Esto evita el envío de correo no deseado a toda la base de usuarios y asegura una retención mejor.

#### Comercio móvil

Recopile las categorías de productos más visitadas en la aplicación y diríjase a la audiencia para promocionar un descuento o un nuevo producto de una de esas categorías que sea más probable que compre el usuario final. Intente aumentar los ingresos. Nuevamente, el objetivo es no enviar correo no deseado.

#### Juegos

Recopile el nivel de juego de un usuario final y el tiempo dedicado en un período determinado para dirigirse a la audiencia que podría estar estancada y que probablemente pasaría al siguiente nivel con una oferta exclusiva.

Realice comunicaciones sobre eventos concretos con un incentivo para los usuarios que llevan cierto tiempo sin jugar con el fin de recuperarlos.

#### Minoristas

Recopile los productos o marcas que consumirá una audiencia con más probabilidad en función del comportamiento o de gustos y consiga dirigir a la audiencia a la tienda para aumentar los ingresos de ventas.

#### Banca

Recopile datos de los usuarios finales que crearon una cuenta en el momento del lanzamiento de la aplicación. Póngase por objetivo implementar una estrategia de bienvenida con notificación de inserción orientada y aumentar el número de suscripciones de cuenta.

### ¿Cómo crear un plan de etiqueta eficaz?

Un plan de etiqueta debe ser como una descripción de la ruta del usuario o un tipo de flujo de trabajo de la aplicación, que proporciona todas las etiquetas necesarias (datos) que deben recopilarse para disponer de suficientes análisis para comprender el comportamiento del usuario y segmentar correctamente la base de usuarios. No se trata de un proceso técnico. Por lo tanto, los vendedores son capaces de especificar los datos que desean recopilar en función de su estrategia de Mobile Engagement.

Como mínimo hay que etiquetar todas las pantallas (denominadas *Actividades* en Mobile Engagement) de una aplicación. Esto ayuda a determinar la ruta de acceso de usuario.

Una actividad puede insertar *eventos* que recopilan información de acciones como hacer clic en un botón. Esto permite recopilar la interacción dentro de la aplicación. Por lo tanto, los vendedores pueden saber qué pantallas visitan los usuarios y qué hacen.

`Jobs` son acciones con una duración concreta. Esto es muy útil para que el vendedor sepa, por ejemplo, cuánto tiempo tarda un usuario en crear una cuenta o en iniciar sesión. Esto también puede resultar útil para los desarrolladores al supervisar cuánto tiempo se tarda en llamar a un servicio web.

`Errors` se pueden controlar para saber si los usuarios tienen problemas en la aplicación. Por ejemplo, casos de problemas de conexión frecuentes.

Todo este tipo de datos puede ampliarse con parámetros (*información adicional* en Mobile Engagement), lo que permite recopilar datos dinámicos de la aplicación. Esto es importante para permitir una segmentación precisa. Por ejemplo, los vendedores pueden segmentar al usuario en función del tipo de contenido consumido. El tipo de contenido será la información dinámica de una actividad o un evento.

*Información en la aplicación* son los datos que permiten confirmar el estado de la aplicación o del usuario en tiempo real. Esto también sirve para categorizar una base de audiencia y dirigirse a ella rápidamente. Por ejemplo, puede usar un estado true/false sobre si el usuario inició sesión o no, o sobre la fecha de expiración de la suscripción.

#### Ejemplo de etiquetas

*Caso de uso: segmente el comportamiento de la audiencia para dirigirse al usuario final adecuado con el contenido correcto de notificación de inserción*

1.	Enviar notificación de inserción para promocionar una categoría de productos: recopile datos de comportamiento para segmentar la audiencia según la categoría de producto visitada x veces en un período determinado o un elemento específico agregado a un carro. Los datos recopilados permitirán segmentar y enviar una notificación de inserción a la audiencia adecuada.
2.	Clasificar la aplicación: recopile datos según el contenido compartido por la audiencia en una red social. Objetivos para segmentar la audiencia determinando quiénes son los *embajadores* de la aplicación. Los embajadores son la mejor audiencia de la aplicación a quien puede pedir, con una notificación de inserción en la aplicación, que valore la aplicación con 5 estrellas en la tienda.

	![][1]

*Caso de uso: datos declarativos*
1.	Segmente las noticias de alerta: recopile datos declarativos para segmentar la audiencia según sus preferencias. Permite enviar notificaciones de inserción de un tema concreto que realmente le interesa a una audiencia concreta.
2.	Segmente la audiencia según el estado de inicio de sesión. Recopile datos para saber si un usuario está conectado o ha creado una cuenta. Diríjase a usuarios finales que aún no iniciaron sesión y envíe una notificación de inserción para atraer la participación del usuario final. ![][2]

### Pasos siguientes

- Visite [Conceptos de Mobile Engagement] para obtener más información sobre conceptos básicos de Mobile Engagement.
- Visite [Creación de una aplicación de Azure Mobile Engagement](mobile-engagement-create.md) para crear una colección de aplicaciones de Mobile Engagement en Azure y comenzar a administrar las aplicaciones con el portal de Mobile Engagement.
- Visite [Azure Mobile Engagement - Guía de introducción con procedimientos recomendadas](mobile-engagement-getting-started-best-practices.md) para acceder a los detalles
- Visite [Implementación de Mobile Engagement con una aplicación de juegos](mobile-engagement-gaming-scenario.md) para obtener información sobre la implementación de Mobile Engagement con una aplicación de juegos de ejemplo. 
- Visite [Implementación de Mobile Engagement con una aplicación de medios](mobile-engagement-media-scenario.md) para obtener información sobre la implementación de Mobile Engagement con una aplicación multimedia de ejemplo. 
- Visite [Tutoriales] para obtener más información sobre la implementación.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Conceptos de Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Tutoriales]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

<!---HONumber=AcomDC_0302_2016-->
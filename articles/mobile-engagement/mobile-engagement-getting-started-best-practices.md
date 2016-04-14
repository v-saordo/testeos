<properties 
	pageTitle="Azure Mobile Engagement - Guía de introducción con prácticas recomendadas"
	description="Guía de introducción para Azure Mobile Engagement y prácticas recomendadas para la incorporación" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="12/11/2015"
	ms.author="wesmc"/>

# Azure Mobile Engagement - Guía de introducción con prácticas recomendadas

## Información general

**La pantalla del móvil es un espacio repleto:** en 2013, un estudio reveló que un dispositivo móvil medio tenía 27 aplicaciones instaladas. Los usuarios solían pasar normalmente unas 30 horas al mes en sus aplicaciones. La mayor parte de este tiempo se dedicaba a las redes sociales y a los juegos (unas 20 horas). En 2014, el mercado de Android tenía alrededor de 1,5 millones de aplicaciones entre las que podían elegir los usuarios. La tienda de Apple contenía aproximadamente 1,2 millones de aplicaciones. El uso de aplicaciones móviles sigue aumentando a medida que los desarrolladores compiten en este mercado en expansión.

El usuario de móvil medio instalará y desinstalará aplicaciones con mucha frecuencia en función de sus intereses y experiencias en las aplicaciones. Para determinar el éxito de una aplicación, resulta crucial saber algo más que cuántos usuarios van a instalar la aplicación. Es importante conocer la utilidad de la aplicación y si esa tendencia de uso está cambiando. Las siguientes preguntas son fundamentales:

- ¿Están los usuarios empezando a pensar que su aplicación no es interesante o está obsoleta? 
- ¿Cuántos usuarios han dejado de usar su aplicación? 
- ¿Está la tendencia de adquisición de la aplicación subiendo o bajando?
- ¿Pueden los usuarios completar o no los flujos de trabajo debido a problemas con la aplicación o por falta de interés? 
- ¿Puede mantener la utilidad y pertinencia de su aplicación agregando nuevo contenido para su base de usuarios? 
- ¿Este nuevo contenido será el mismo para todos los usuarios o se centrará en segmentos de usuario según el comportamiento de la aplicación? 
 
Respuestas a preguntas similares a estas pueden ayudar a ampliar la duración y los ingresos de su aplicación. También pueden ayudarle a definir y mantener su base de usuarios.

Las aplicaciones relacionadas con multimedia suelen tener algunos de los máximos índices de retención entre los usuarios. Una de las razones de esto es que se proporcionan constantemente nuevos contenidos a los usuarios. La adopción temprana de las notificaciones push útiles dirigidas a un segmento de usuarios suele tener un gran impacto en la retención de la aplicación.

El programa Azure Mobile Engagement está diseñado para ayudar a aumentar la vida y la retención de la aplicación al proporcionar un método para recopilar y analizar información detallada sobre el uso de la aplicación. Le permitirá clasificar su base de usuarios en función del comportamiento y crear campañas concretas para entregar notificaciones push y mensajes de la aplicación para segmentos de usuarios identificados. Los indicadores clave de rendimiento (KPI) miden la actividad de los usuarios en los distintos aspectos de la aplicación. Azure Mobile Engagement proporciona los métodos que necesita para determinar estos KPI. Ayuda a aumentar la rentabilidad de la inversión (ROI) al proporcionar la infraestructura que necesita para aumentar el compromiso con la aplicación móvil.

Para sacar el máximo partido de Azure Mobile Engagement, debe comenzar con un plan de compromiso bien diseñado. El plan le ayudará a identificar los datos pormenorizados que necesita para poder segmentar la base de usuarios. Esto puede basarse en el comportamiento y las experiencias de la aplicación. Para que el plan sea correcto, es recomendable definir claramente los KPI que medirán los objetivos de la aplicación. Con unos indicadores de rendimiento claramente definidos, puede insertar fácilmente la lógica necesaria en la aplicación para recopilar datos específicos que usará para analizar y evaluar los KPI. Este tema es una guía de prácticas recomendadas para definir los KPI que va a utilizar con el plan de compromiso.


## Paso 1: Definición de los KPI para que se ajusten al modelo BET


Definir correctamente los KPI puede ser una tarea difícil de realizar. Las aplicaciones diseñadas para diferentes sectores tienen sus propias características y objetivos. Esto tiende a confundir el enfoque. Para evitar esta situación, hay que clasificar los objetivos y los KPI en tres categorías principales: **Negocio**, **Compromiso** y **Técnico**. Esto es lo que denominamos **modelo BET**.

Un buen plan puede tener objetivos con KPI que miden los éxitos en cada una de las siguientes categorías del modelo BET.


#### KPI de negocio

Los KPI de negocio son los más fáciles de crear. Probablemente ya los había definido de algún modo al planear su aplicación móvil. Estos KPI ayudan en general a medir los ingresos y la rentabilidad de la inversión de la aplicación. En la lista siguiente se proporcionan algunos ejemplos de KPI de negocio que le pueden ayudar a definir los indicadores de rendimiento:

- KPI de negocio de medios
	- Número de anuncios pulsados
	- Número de visitas de la página por usuario
	- Número de suscripciones actuales
- KPI de negocio de juegos
	- Número de compras de la aplicación
	- Ingreso medio por usuario (ARPU)
	- Tiempo empleado por sesión
	- Días jugados y actuales en el nivel de juego
- KPI de negocio de comercio electrónico
	- Días de uso de la aplicación
	- Ingreso medio por usuario (ARPU)
	- Importe medio en carro de la compra al finalizar la compra
	- Categoría de productos para la mayoría de vistas y compras
- KPI de negocio de bancos y seguros
	- Número de cuentas
	- Características activadas
	- Páginas de oferta visitadas
	- Alertas pulsadas o activadas	   



#### KPI de compromiso

Un KPI de compromiso es un indicador de rendimiento que mide el nivel de compromiso de los usuarios. Las tendencias en este campo ayudan a determinar el período de retención de la aplicación. Estos son algunos indicadores de rendimiento de ejemplo para este tipo de KPI:

- Usuarios activos en los últimos siete días
- Número de usuarios inactivos en los últimos siete días
- Número de usuarios que no usaron la aplicación en 30 días  

Algunos factores externos obvios pueden influir en los indicadores en este campo. Por ejemplo, puede pensar que un dispositivo móvil está con el usuario en todo momento. Esto puede ser o no verdad. Una aplicación de juegos tiende a usarse más en vacaciones cuando un jugador tiene más tiempo de jugar porque está fuera del trabajo o de la escuela.

Unos KPI bien definidos en esta categoría deberían ayudarle a medir la relación entre la aplicación y sus clientes.



#### KPI técnicos

Los indicadores de rendimiento de esta categoría le ayudan a determinar si la aplicación se está comportando correctamente, se cuelga o se bloquea. Estos indicadores pueden medir el estado de la aplicación y determinar los problemas de uso que pueden impedir su uso. La información recopilada para esta categoría también puede contener información de rendimiento de cierta importancia para los equipos de marketing. Los datos también pueden ser útiles para solucionar problemas de TI y ayudan a que los equipos de soporte técnico identifiquen errores no notificados.
 
A continuación se muestran algunos ejemplos de KPI técnicos:

- Recuento e información de excepciones controladas o no controladas 
- Marca de tiempo del último bloqueo
- Último botón pulsado o última página visitada
- Uso de memoria de la aplicación
- Velocidad de fotogramas de la aplicación
- Versión del sistema operativo en el que se está ejecutando la aplicación
- Versión de la aplicación

Definir estos KPI ayuda a medir el rendimiento de la aplicación y a detectar posibles errores. Este indicadores deberían ayudar a reducir el tiempo que necesita para ofrecer una corrección a sus clientes. También pueden ayudar a definir un segmento de usuarios que han tenido problemas específicos. Puede usar esta segmentación de usuarios para crear campañas y proporcionar notificaciones sobre posibles promociones y correcciones disponibles que ayuden a recuperar la satisfacción del cliente.


#### Ejercicio 1 del cuaderno de estrategias: Creación del panel de KPI

Al definir la estrategia de marketing, los KPI deben presentar una vista para cada uno de los objetivos principales. Los puntos de datos deben estar claramente definidos, ya que le permitirán recopilar información fundamental para supervisar la aplicación y el comportamiento del usuario final.

Cree un panel de KPI que contenga la siguiente información

1.	¿Cuáles son los KPI para la aplicación?
2.	¿Qué puntos de datos voy a usar para representar cada KPI?
3.	¿Dónde se encuentran estos datos para mi aplicación (es decir, pantalla, configuración, sistema...)?
4.	¿Se puede reproducir una secuencia de compromiso para este KPI?

Puede usar la hoja de cálculo **KPI Builder** (Generador de KPI) de [Media Playbook Template][Media Playbook link] (Plantilla del cuaderno de estrategias de medios) para ver ejemplos y recomendaciones.



## Paso 2: El programa de compromiso


Un gran programa de compromiso de móviles debe considerarse como un componente clave de la aplicación. Debe incluir indudablemente un magnífico programa de bienvenida que se ejecute para un usuario durante los primeros días de uso de una aplicación. Suele tener un efecto muy positivo en el compromiso y la retención de la aplicación. Los estudios demuestran que la mayoría de los usuarios dejan de usar una aplicación pocos días después de su instalación. Desea esforzarse por alcanzar o superar el interés ante las expectativas del cliente desde el principio mientras el usuario sigue aún centrado en la aplicación. Asegúrese de que presenta el valor y las ventajas más importantes de la aplicación a los clientes.


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Las notificaciones push son la mejor forma de lograr un compromiso temprano por parte de los usuarios de dispositivos móviles. Sin embargo, se debe tener mucho cuidado al segmentar los usuarios para las notificaciones push. Porque si un usuario se siente como si recibiera correo no deseado o notificaciones sin interés, puede tener graves consecuencias. En unos pocos clics, un usuario puede borrar la aplicación para no volver a ella jamás. El usuario debe recibir un valor muy personalizado de la aplicación en lugar de un correo no deseado genérico.

Una vez que los usuarios están comprometidos activamente, el programa de compromiso puede ayudar a controlar otros aspectos de la aplicación.

Por ejemplo, puede configurar una campaña que solicite a los usuarios activos que califiquen la aplicación. Puesto que este segmento de usuarios es el más activo y tiene la mayor experiencia con la aplicación, puede esperar que su evaluación sea muy precisa. Una vez que tenga una evaluación de la aplicación alta, esto puede ayudarle a impulsar la descarga orgánica de la aplicación, así como a reducir los costos de adquisición de nuevos clientes.



#### Secuencia de compromiso


Un programa de compromiso global incluye distintas secuencias de compromiso. Cada secuencia está dirigida a alcanzar varios objetivos.


###### Secuencia push de ciclo de vida


Los objetivos de una secuencia push de ciclo de vida son diferentes en función del ciclo de vida del compromiso del usuario con la aplicación. Un usuario determinado puede ser nuevo, inactivo o muy activo. En diferentes etapas del ciclo de vida de un compromiso, los usuarios pueden beneficiarse de contenido nuevo en forma de sugerencias o vínculos a la documentación.

Por ejemplo un nuevo usuario necesita ayuda para orientarse hacia una aplicación o beneficiarse de un nuevo incentivo de usuario similar al siguiente la primera vez que inicie la aplicación...

*"¡Nos alegramos de verle por aquí! No se olvide de iniciar sesión para obtener el primer mes gratis."*


###### Secuencia push de comportamiento

La secuencia push de comportamiento tiene por objetivo aumentar el uso en función del comportamiento de los usuarios recopilado para la aplicación.

Por ejemplo, un usuario muy activo de una aplicación de fútbol de fantasía puede beneficiarse de la siguiente notificación push...

*"Juan, ¡eres un verdadero hincha del fútbol! Inicie sesión en nuestra sección Liga Nacional y obtenga un acceso gratuito a la Liga de Campeones."*


###### Secuencia push de alertas

Los usuarios sabrán apreciar las noticias relevantes de sus intereses. Una secuencia push de alertas mejora el compromiso enviando alertas basadas en los intereses que un usuario demuestra tener claramente. Esto puede ser explícito cuando un usuario selecciona sus propios intereses en la aplicación. Puede también determinarse implícitamente basándose en los datos recopilados durante la interacción del usuario con la aplicación.

Por ejemplo, el usuario de una aplicación de comercio electrónico puede comprar con regularidad una marca específica de café que usted captó con un KPI de negocio. La alerta siguiente puede mejorar el compromiso de este usuario con la aplicación.
 
*"Hola, María, una de sus marcas favoritas de café estará en venta con un 25% de descuento la primera semana de septiembre de 2015. Le apreciamos mucho como cliente y queríamos asegurarnos de que usted lo supiera."*

###### Secuencia push de retención

El objetivo de esta secuencia es retener a los usuarios mediante campañas de notificación push repetitivas para impulsar un hábito regular de compromiso con la aplicación. Esto puede ayudar a aumentar la retención de la aplicación si el usuario aprecia las interacciones.

Por ejemplo, el usuario de una aplicación relacionada con el deporte puede recibir la siguiente notificación push semanalmente basándose en los equipos favoritos del usuario:

*"Para poder ganar 200 puntos, vote si el Real Madrid ganará el partido contra el FC Barcelona esta semana."*


#### Enfoque de 3W

Dominar las distintas secuencias push le ayudará a comprometerse con los usuarios finales. Sin embargo, deberá usar el enfoque de las 3 W para personalizar las notificaciones. El enfoque de las 3 W debe abordar las preguntas Quién, Qué y Cuándo (Who, What y When en inglés) para cada notificación. Si responde claramente a estas tres preguntas, sus notificaciones estarán correctamente enfocadas hacia el compromiso.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### Quién: segmento de usuarios que recibirá los mensajes

Las notificaciones push a los usuarios deben considerarse como un canal de comunicación muy confidencial. Asegúrese de que las notificaciones que desea enviar a un segmento de usuarios estén bien enfocadas hacia los intereses de ese segmento de usuarios. Es muy probable que una notificación mal dirigida tenga un efecto negativo en un usuario. Puede considerarla como correo no deseado y llevar a la desinstalación de la aplicación.

Use una combinación de criterios técnicos y de comportamiento específicos al definir los segmentos de usuarios que van a recibir las notificaciones. Un ejemplo sencillo al definir un segmento de usuarios puede ser parecido a la siguiente declaración:

"Todos los usuarios que iniciaron una aplicación móvil por primera vez hace tres días, visitaron la página de inicio de sesión dos veces sin terminar de completar el inicio de sesión".
 
Esta declaración ayuda a identificar los datos que puede necesitar recopilar para dar apoyo a un escenario específico.


###### Qué: el mensaje que se va a enviar

**Tono**

En sus compromisos, use un tono que sea adecuado para los usuarios segmentados. Esto es, indudablemente, una estupenda forma para conectar con los usuarios finales y promover el interés de un usuario por la aplicación.

**Redireccionamiento**

Una notificación push puede usarse para algo más que para la apertura de la aplicación. Si el mensaje de notificación proporciona un contexto como noticias de difusión o la promoción de un producto, esta notificación puede tener un vínculo profundo directo con el contenido preciso de la aplicación. Para ello, debe crear un esquema de direcciones URL para que la aplicación pueda administrar el redireccionamiento. Cuando trabaja en sus secuencias de compromiso, esto es un paso importante del que no se debe olvidar.

También es posible administrar el redireccionamiento para otros sistemas. Por ejemplo, con una dirección URL de acción es posible redirigir a los usuarios finales a muchos otros sistemas, incluidos los siguientes:

- Un sitio web
- Un buzón de correo electrónico con mensajes ya configurados
- Un buzón de SMS
- Un servicio de marcado telefónico
- Directamente al almacén de la aplicación para la evaluación de la aplicación. 

Esto ofrece muchas oportunidades para aumentar el compromiso de los usuarios finales y crear reglas automáticas para mejorar el rendimiento.


**Formato y contenido**

Hay distintos tipos y formatos de notificación push:

1. **Anuncios**: permite enviar mensajes publicitarios a los usuarios en diferentes momentos (fuera de la aplicación, en la aplicación o en cualquier momento).
2. **Sondeos**: permite recopilar información de los usuarios finales mediante el planteamiento de preguntas. Estas respuestas están luego disponibles al crear criterios para los usuarios finales de destino.
3. **Inserciones de datos**: permite enviar un archivo de datos binario o Base 64 para actualizar la aplicación. La información contenida en una inserción de datos se envía a la aplicación para personalizar la experiencia de los usuarios en la aplicación. La aplicación debe estar diseñada para admitir los datos de una inserción de datos.
4. **Iconos (solo en Windows Phone)**: permite usar el servicio de notificaciones push de Microsoft (MPNS) para enviar notificaciones push nativas que contienen datos XML (admitido a partir de SDK versión 0.9.0. La carga final de los iconos no puede superar los 32 kilobytes). El mensaje aparece directamente en el icono del panel.
5. **Vista web** : una vista web es una ventana emergente con contenido web. Este elemento emergente aparece cuando el usuario final hace clic en la notificación de inserción. Una vista web le permite interactuar más con el usuario final.
 
>[AZURE.NOTE]Asegúrese de que el contenido que se va a enviar como notificación push cumple con las directrices de la plataforma correspondiente (iOS, Android, Windows) para desarrollar aplicaciones y enviar notificaciones push.

 


###### Cuándo: el plazo de la campaña.

¿Cuándo es el mejor momento para activar una campaña que desencadena notificaciones push? ¿Debe ser manual o automática? ¿Debe ser periódica? Determinar la frecuencia o el momento adecuado es esencial para aumentar el compromiso de los usuarios y obtener los mejores resultados. Para cada escenario y secuencia de compromiso, debe especificar cuál será el mejor momento para enviar notificaciones push. Estos son algunos ejemplos:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Si va a enviar a diario muchas notificaciones, debe tener en cuenta que los usuarios pueden percibir las comunicaciones como correo no deseado.

Azure Mobile Engagement proporciona dos maneras de intentar evitar que las comunicaciones se perciban como correo no deseado. En primer lugar, use una segmentación concreta para asegurarse de que no se dirige a los mismos usuarios. Además, Azure Mobile Engagement proporciona una característica de "cuota". Esta característica puede limitar las notificaciones enviadas para una campaña. Por ejemplo, establecer una cuota predeterminada en 5 por semana garantizará que un usuario incluido como parte del segmento de usuarios de una campaña no reciba más de cinco notificaciones en esa semana.





#### Ejercicio 2 del cuaderno de estrategias: Creación del programa de compromiso

Dedique un tiempo a resumir los objetivos y a definir las campañas que piensa desarrollar con secuencias específicas. Asegúrese de aplicar el enfoque de las 3 W a las notificaciones de sus campañas.

Puede usar la hoja de cálculo **Engagement Program** (Programa de compromiso) de [Media Playbook Template][Media Playbook link] (Plantilla del cuaderno de estrategias de medios) para ver ejemplos y recomendaciones.


## Paso 3: Integración de la aplicación


#### Creación de un plan de etiquetas

Para integrar Azure Mobile Engagement en la aplicación, debe crear un plan de etiquetas. El plan de etiquetas constituye la piedra angular del proyecto. Define la relación entre las especificaciones de marketing, el flujo de trabajo de la aplicación y los datos de etiquetas reales recopilados en la aplicación para medir los KPI. Indica qué análisis podrá ver en el portal. También ayuda a definir segmentos de usuarios y a enviar notificaciones push enfocadas hacia el compromiso de los usuarios finales. Una vez definido el plan de etiquetas, agregar el código para integrarlo en su aplicación es sencillo mediante el SDK de Azure Mobile Engagement.

Un plan de etiquetas no debe etiquetarlo todo en una aplicación. Solo debe incluir los datos de etiquetas que forman parte de su estrategia de Mobile Engagement. Esto probablemente variará de una aplicación a otra. La plantilla [Media Playbook Template][Media Playbook link] (Plantilla del cuaderno de estrategias de medios) proporcionada por Azure Mobile Engagement le ayuda a crear un plan de etiquetas con un método determinado. Use la hoja de cálculo **Tag Plan** (Plan de etiquetas) como guía para crear su plan de etiquetas.

Al definir una sección de etiqueta en la hoja de cálculo, sea muy específico. Esto es muy importante para evitar confusiones. Proporcione detalles de cada escenario esperado en el que se enviará cada etiqueta. Incluya el nombre de la actividad en la que está insertada cada etiqueta. Debería estar todo incluido en la parte **Informative** (Informativa) de la hoja de cálculo. La hoja de cálculo Tag Plan (Plan de etiquetas) debe ser la referencia principal para la comprobación de la prueba.

En la sección **Data to collect** (Datos para recopilar), el equipo de desarrollo debe buscar los tipos, nombres, valores y pares de clave/valor de información adicional necesarios para cada etiqueta que se insertará en la aplicación.

Se recomienda revisar el plan de etiquetas con todos los equipos asociados al proyecto. Realice las correcciones necesarias y confirme que todo está claro para los equipos de desarrollo y marketing.

La hoja de cálculo **Statement of work** (Declaración de trabajo) se puede usar para que sirva de guía a todos los implicados en el proyecto.


#### Tipo de datos

Estos son los tipos comunes de datos compatibles con Azure Mobile Engagement.

###### Dispositivos y usuarios

Azure Mobile Engagement identifica a los usuarios mediante la generación de un identificador único para cada dispositivo. Este identificador se denomina identificador de dispositivo (o deviceid). Se genera de tal forma que todas las aplicaciones que se ejecutan en el mismo dispositivo comparten el mismo identificador de este tipo.

###### Sesiones y actividades

Una sesión es una instancia de la aplicación que ejecuta un usuario. La sesión abarca desde el momento en que el usuario inicia la aplicación hasta que se detiene.

Una actividad es una agrupación lógica de un conjunto de acciones que puede realizar la aplicación. Suele ser una pantalla concreta de la aplicación pero puede ser que cualquier cosa definida por la lógica de la aplicación. Como mínimo, debe etiquetar cada pantalla o actividad de la aplicación. Esto le permitirá comprender la ruta del usuario.


###### Eventos

Los eventos se usan para notificar la interacción del usuario con la aplicación. Pueden ser acciones instantáneas, como compartir contenido o iniciar un vídeo. El etiquetado de eventos le proporcionará recopilaciones de datos que muestran cómo interactúan los usuarios con la aplicación.

###### Trabajos

Los trabajos se usan para notificar acciones que tienen una duración. Algunos ejemplos pueden incluir:

- Ejecución de llamadas a la API
- Tiempo de visualización de anuncios
- Duración de tareas en segundo plano 
- Duración del proceso de compra
- Reproducción de un vídeo


###### Errors

Los errores se usan para notificar problemas detectados por la aplicación. Por ejemplo, acciones de usuario incorrectas o errores de llamadas a la API.

###### Información de la aplicación

La información de la aplicación (App-Info) se usa para etiquetar los datos relacionados con la experiencia de un usuario con una aplicación. Se genera mediante la interacción de un usuario con la aplicación.

Para una clave app-info determinada, Azure Mobile Engagement solo realiza un seguimiento del valor más reciente (sin historial). App-info revela el estado de la aplicación o de los usuarios finales. Por ejemplo, el estado de inicio de sesión o el grupo de productos favoritos de un usuario.

###### Datos de bloqueo

Datos de bloqueo recopilados automáticamente por los errores de aplicación de informes de SDK de Mobile Engagement no controlados por la aplicación. Por ejemplo, una excepción no controlada que se produzca.


###### Datos adicionales

Los eventos, errores, actividades y trabajos se pueden mejorar mediante parámetros. Se trata de información adicional que un desarrollador puede proporcionar como datos específicos de la aplicación. Esto es importante para definir una segmentación específica.

Por ejemplo, el valor de una etiqueta "article" le permitirá segmentar los usuarios finales según quién vea ese artículo determinado. Sin embargo, quizás no sea suficiente. Sería mejor si esa misma etiqueta "article" incluyera también información adicional, como "news\_category" dentro de una actividad. Esto podría ser útil para determinar dinámicamente las categorías favoritas para el usuario.

La información adicional se notifica como par clave/valor. En el ejemplo de esta aplicación de medios, la información adicional para "news\_category" sería el valor de esa categoría. Por ejemplo, "deporte", "economía" o "política".





#### Etiquetas e integración de SDK 

Para obtener instrucciones paso a paso a fin de integrar el SDK de Azure Mobile Engagement en la aplicación, siga la documentación de [Integración del SDK de Windows Universal Apps Engagement](mobile-engagement-windows-store-integrate-engagement.md) en el sitio web de Azure. Elija la plataforma de destino de los vínculos en la parte superior de la página.

Se recomienda crear proyectos para las dos aplicaciones creadas por encima de Azure Mobile Engagement. Una para el almacenamiento provisional de desarrollo y prueba y otra, para el almacenamiento provisional de producción. Su equipo de TI puede promover el almacenamiento provisional de la prueba a la producción cuando las pruebas de aceptación por el usuario se realicen correctamente.



#### Pruebas de aceptación por el usuario (UAT)

Las pruebas de aceptación por el usuario (UAT) implican asegurarse de que todo funciona tal como se diseñó. Los flujos de trabajo pueden realizarse y recopilar todos los datos necesarios en función de su plan de etiquetas:
 
- El etiquetado de información debe implementarse según los conceptos de AZME documentados.
- Se recopila toda la información que necesita (incluidos el valor de información adicional, el valor de información de la aplicación).
- Coincidencias de nomenclatura según el plan de etiquetas.
- No hay etiquetas duplicadas enviadas.

Pruebe minuciosamente todos los tipos de comportamiento de notificación que insertó en la aplicación.

- Anuncios, sondeos, inserciones de datos fuera de la aplicación y en la aplicación
- Vistas web y de texto
- Actualización de notificaciones y categorías



#### Configuración

La configuración de Azure Mobile Engagement es muy sencilla. Toda la documentación relacionada con la interfaz de usuario está disponible en el sitio web de Azure Mobile Engagement [Cómo navegar por la interfaz de usuario](mobile-engagement-user-interface-navigation.md).

Se recomienda comenzar por la configuración de los roles adecuados y las pertenencias a roles para los usuarios del proyecto. Esto ayuda a administrar el acceso adecuado a la plataforma para todos los usuarios. Los roles pueden incluir:

- Administradores
- Desarrolladores
- Lectores 

Posteriormente: - Registre el Id. de dispositivo para probarlo en su propio dispositivo. - Vaya a la configuración de la cuenta y configure la zona horaria para que la hora de envío de gráficos y notificaciones esté establecida para su zona horaria. - Vaya a la configuración de la aplicación y registre la "App-info" necesaria para llegar a los usuarios finales que estén a su alcance.

Para obtener más información sobre cómo ejecutar la primera campaña de notificación push, consulte [Cómo empezar a usar y administrar inserciones para llegar a los usuarios finales](mobile-engagement-how-tos.md).



## Conclusión


Los programas de compromiso son iterativos y debe mejorarlos continuamente según vaya sabiendo lo que funciona mejor para su aplicación.

Al principio, al ir desarrollando su experiencia con las estrategias de compromiso, no intente crear una estrategia completa de compromiso global. Adopte un enfoque paso a paso para identificar los KPI y saber cómo sacar partido de ellos. La estrategia de contratación será única para cada aplicación.

Una vez conseguida una mayor experiencia, puede agregar lo siguiente a sus programas de compromiso:

- Seguimiento: adquiere usuarios y probablemente defina orígenes de recopilación de datos. Azure Mobile Engagement se puede vincular a orígenes de recopilación de datos. Le permite supervisar el rendimiento de cada origen. Esta información será interesante para aumentar al máximo la inversión de la adquisición. 

- Pruebas A/B: es una parte esencial del programa de compromiso. Cada aplicación tiene sus propias características específicas. Con pruebas A/B, puede mejorar el programa de compromiso.

- Ubicación geográfica: se trata de una gran oportunidad para las marcas. Gracias a esta característica puede ponerse en contacto con los usuarios en el lugar correcto y la hora adecuada. Se recomienda comprobar que ha reunido suficientes datos de comportamiento de los usuarios finales antes de comenzar a usar la ubicación geográfica.

- Inserción de datos: la inserción de datos es una inserción invisible. La inserción de datos permite personalizar la aplicación basándose en el comportamiento de los usuarios finales. Por ejemplo, si un segmento de usuarios consulta a menudo productos de alta tecnología, el propietario de la aplicación puede enviar una inserción de datos que se usará para personalizar su página principal con contenido de alta tecnología.



## Pasos siguientes

- [Creación de una cuenta de Azure Mobile Engagement](mobile-engagement-create-account.md)
- Visite [Definición de la estrategia de Mobile Engagement](mobile-engagement-define-your-mobile-engagement-strategy.md) para aprender sobre la definición de la estrategia de Mobile Engagement.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_1217_2015-->
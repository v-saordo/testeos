<properties
   pageTitle="Escenarios comunes del Catálogo de datos de Azure"
   description="Información general de escenarios comunes del Catálogo de datos de Azure, incluido el registro y la detección de orígenes de datos de gran valor, la habilitación de inteligencia empresarial de autoservicio y la captura de conocimiento tribal existente acerca de los orígenes de datos y procesos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="maroche"/>


# Escenarios comunes del Catálogo de datos de Azure

Este artículo presenta escenarios comunes en los que el Catálogo de datos de Azure puede ayudar a las organizaciones a obtener más valor de sus orígenes de datos existentes.

## Escenario número 1: registro de orígenes de datos centrales

Las organizaciones suelen tener un determinado número de orígenes de datos de gran valor. Entre estos orígenes de datos se incluye la línea de sistemas OLTP de negocio, almacenes de datos e inteligencia empresarial y bases de datos de análisis. A menudo el número de sistemas y la superposición entre los sistemas aumenta con el tiempo a medida que las necesidades de las empresas evolucionan y que la propia empresa evoluciona mediante fusiones y adquisiciones.

A menudo es difícil para los usuarios saber dónde buscar los datos en estos orígenes de datos. Preguntas como las siguientes son demasiado comunes:

- De los tres sistemas de recursos humanos usados dentro de la empresa, ¿cuál debo usar para crear este tipo de informe?
- ¿Dónde debo acceder para obtener las cifras de ventas certificadas del año fiscal que acaba de terminar?
- ¿A quién debo preguntar o cuál es el proceso que debo seguir para obtener acceso al almacén de datos?
- No sé si estos números son correctos. ¿A quién puedo preguntar para obtener información sobre cómo se deben usar estos datos antes de compartir este panel con mi equipo?

En este escenario, el Catálogo de datos de Azure puede resultar de ayuda. Los orígenes de datos centrales, de alto valor administrados mediante TI que se usan en toda la organización suelen ser el punto de partida lógico para rellenar el catálogo. Aunque cualquier usuario puede registrar un origen de datos, hacer arrancar el catálogo con los orígenes de datos con más probabilidades de proporcionar valor al mayor número de usuarios ayudará a impulsar la adopción y el uso del sistema. Para los clientes que empiezan a usar el Catálogo de datos de Azure, identificar y registrar los orígenes de datos clave usados por equipos diferentes de consumidores de datos puede ser el primer paso para alcanzar el éxito.

Este escenario también presenta una oportunidad para anotar los orígenes de datos de alto valor para que sea más fáciles comprenderlos y tener acceso a ellos. Un aspecto clave de este esfuerzo es incluir información sobre cómo los usuarios pueden solicitar acceso al origen de datos. Catálogo de datos de Azure permite que los usuarios proporcionen la dirección de correo electrónico del usuario o equipo responsable de controlar el acceso al origen de datos, los vínculos a documentación o herramientas existentes o texto libre que describe el proceso de solicitud de acceso. Con esta información en el catálogo, los usuarios que detectan los orígenes de datos registrados pero todavía no tienen permiso de acceso a ellos pueden solicitar fácilmente acceso a través de los procesos definidos y controlados por los propietarios de los orígenes de datos.

## Escenario número 2: inteligencia empresarial de autoservicio

Aunque las soluciones de inteligencia empresarial corporativa tradicionales continúan siendo una parte inestimable de los entornos de datos de muchas de las organizaciones, el ritmo cambiante de la empresa ha provocado que la BI de autoservicio resulte cada vez más importante. La BI de autoservicio permite a las personas que trabajan con información y a los analistas crear sus propios informes, libros y paneles sin necesidad de usar un equipo de TI central ni estar restringidos por la programación y la disponibilidad de ese equipo de TI.

En escenarios de BI de autoservicio, es habitual que los usuarios combinen datos de varios orígenes, muchos de las cuales puede que no hayan sido usados anteriormente para BI y análisis. Aunque es posible que algunos de estos orígenes de datos ya sean conocidos, con frecuencia hay un proceso para descubrir lo que debe tener lugar para buscar y evaluar posibles orígenes de datos para una tarea determinada.

Tradicionalmente, este proceso de descubrimiento es de tipo manual: los analistas usarán sus conexiones de red del mismo nivel para identificar a otras personas que trabajan con los datos que se buscan. En cuanto se encuentra un origen de datos, este se puede usar, pero el proceso se repite de nuevo para cada autoservicio BI posterior, con varios usuarios realizando el mismo proceso manual redundante de detección.

Con Catálogo de datos de Azure, los usuarios pueden interrumpir este ciclo de esfuerzos redundantes. Una vez que se ha descubierto un origen de datos a través de medios tradicionales, un analista puede registrar el origen de datos para que sea más fácilmente detectable en el futuro. Aunque el usuario puede agregar más valor anotando los activos de datos registrados, no es necesario que tenga lugar al mismo tiempo que el registro. Los usuarios pueden contribuir con el tiempo, a medida que sus programaciones lo permitan, agregando valor gradualmente a los orígenes de datos registrados en el catálogo.

Este crecimiento orgánico del contenido del catálogo es un complemento natural al registro por adelantado de orígenes de datos centrales. Rellenar previamente el catálogo con datos que necesitarán muchos usuarios puede ser un incentivo para el descubrimiento y uso inicial. Permitir a los usuarios registrar y anotar orígenes adicionales puede ser una manera de mantener a estos y a sus compañeros involucrados.

También cabe tener en cuenta que aunque este escenario se centra específicamente en BI de autoservicio, los mismos patrones y desafíos también se aplican a proyectos de BI corporativos de gran escala. Cualquier esfuerzo que implica un proceso manual de detección del origen de los datos es un esfuerzo que puede agregar valor a la organización mediante el uso del Catálogo de datos de Azure.

## Escenario número 3: obtención de conocimientos tribales

¿Cómo sé qué datos necesita para hacer su trabajo y dónde encontrar esos datos?

Si ha estado en su trabajo durante un tiempo, probablemente lo sepa. Ha experimentado un proceso de aprendizaje gradual y con el tiempo ha obtenido información acerca de los orígenes de datos que son clave para su trabajo cotidiano.

Cuando se une un nuevo empleado a su equipo, ¿cómo sabe qué datos necesita para hacer su trabajo y dónde encontrarlos?

Lo más probable es que se lo pregunte.

Esta transferencia continua de conocimientos tribal forma parte del proceso de detección del origen de los datos en las organizaciones grandes y pequeñas. Los miembros del equipo más mayores y experimentados han acumulado conocimientos con los años y los miembros del equipo más recientes y han aprendido a preguntarles si tienen preguntas. La información más esencial a menudo solo existe en las cabezas de algunas personas claves y cuando esas personas están de vacaciones o dejan el equipo, la organización se ve afectada.

A veces estos expertos en datos harán esfuerzos por documentar sus conocimientos, compartiéndolos mediante correo electrónico o documentos de Word en un sitio de SharePoint del equipo. Aunque esto puede ser valioso, presenta un nuevo problema de detección: ¿cómo se puede saber qué documentación existe y dónde encontrarla?

Catálogo de datos de Azure proporciona una ubicación para compartir estos conocimiento tribales y para que sean fácilmente reconocibles. Los expertos en datos pueden anotar los activos de datos directamente y también pueden incluir vínculos a documentación existente. Esto no solo permite capturar el conocimiento en sí, sino que también coloca el conocimiento en la misma experiencia que se usa para la detección de orígenes de datos. Cuando alguien usa el catálogo para detectar un origen de datos, no solo encontrará el propio origen, sino que también encontrará los conocimientos que anteriormente solo existían en la mente del propio experto.

<!---HONumber=AcomDC_0224_2016-->
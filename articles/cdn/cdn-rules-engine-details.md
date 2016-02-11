<properties 
	pageTitle="Detalles de características y condiciones de coincidencia del motor de reglas de la Red de entrega de contenido (CDN) de Azure" 
	description="En este tema se muestran descripciones detalladas de las características y las condiciones de coincidencia disponibles para el Motor de reglas de la Red de entrega de contenido (CDN) de Azure." 
	services="cdn" 
	documentationCenter="" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Detalles de características y condiciones de coincidencia del motor de reglas de CDN
	
En este tema se muestran descripciones detalladas de las características y las condiciones de coincidencia disponibles para el [Motor de reglas](cdn-rules-engine.md) de la Red de entrega de contenido (CDN) de Azure.

> [AZURE.NOTE]El motor de reglas requiere el nivel Premium de la CDN. Para más información sobre las características de los niveles Estándar y Premium de la CDN, vea [Información general sobre la Red de entrega de contenido (CDN) de Azure](cdn-overview.md).

## Condiciones de coincidencia

Una condición de coincidencia identifica tipos específicos de solicitudes para los que se ejecutará un conjunto de características.

Por ejemplo, puede usarse para filtrar solicitudes de contenido en una ubicación en concreto, solicitudes generadas desde una dirección IP o país en concreto, o por información de encabezado.

### Siempre

La condición de coincidencia Siempre está diseñada para aplicar un conjunto predeterminado de características a todas las solicitudes.

### Dispositivo

La condición de coincidencia Dispositivo identifica solicitudes realizadas desde un dispositivo móvil en función de sus propiedades.

### Ubicación

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de la ubicación del solicitante.

Nombre | Propósito
-----|--------
Número de sistema autónomo (AS) | Identifica solicitudes que se originan en una red determinada.
País | Identifica solicitudes que se originan en países determinados.
 

### Origen

Estas condiciones de coincidencia están diseñadas para identificar solicitudes que señalan al servidor de origen de un cliente o de almacenamiento de CDN.

Nombre | Propósito
-----|--------
Origen de red CDN | Identifica solicitudes de contenido almacenado en el almacenamiento de CDN.
Origen de cliente | Identifica solicitudes de contenido almacenado en el servidor de origen de un cliente específico.


### Solicitud

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de sus propiedades.

Nombre | Propósito
-----|--------
Dirección IP de cliente | Identifica solicitudes que se originan en una dirección IP determinada.
Parámetro de cookie | Busca el valor especificado en las cookies asociadas a cada solicitud.
Regex de parámetro de cookie | Busca la expresión regular especificada en las cookies asociadas a cada solicitud.
Cname perimetral | Identifica solicitudes que señalan a un CNAME perimetral específico .
Dominio de referencia | Identifica solicitudes a las que se haga referencia en los nombres de host especificados.
Literal de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en unos valores especificados.
Regex de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en la expresión regular especificada.
Carácter comodín de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en un valor que coincide con el patrón especificado.
Método de solicitud | Identifica solicitudes por su método HTTP.
Esquema de solicitud | Identifica solicitudes por su protocolo HTTP.

### URL

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de sus direcciones URL.

Nombre | Propósito
-----|--------
Directorio de ruta de acceso URL | Identifica solicitudes por su ruta de acceso relativa.
Extensión de ruta de acceso URL | Identifica solicitudes por su extensión de nombre de archivo.
Nombre de archivo de ruta de acceso URL | Identifica solicitudes por su nombre de archivo.
Literal de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con el valor especificado.
Regex de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con la expresión regular especificada.
Carácter comodín de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con el patrón especificado.
Literal de consulta de dirección URL | Compara la cadena de consulta de una solicitud con el valor especificado.
Parámetro de consulta de dirección URL | Identifica solicitudes que contienen el parámetro de cadena de consulta que se especifique establecido en un valor que coincide con el patrón especificado.
Regex de consulta de dirección URL | Identifica solicitudes que contienen el parámetro de cadena de consulta que se especifique establecido en un valor que coincide con la expresión regular especificada.
Carácter comodín de consulta de dirección URL | Compara los valores especificados con la cadena de consulta de una solicitud.

## Características

Una característica define el tipo de acción que se aplicará al tipo de solicitud identificado con un conjunto de condiciones de coincidencia.

### Access

Estas características están diseñadas para controlar el acceso al contenido.

Nombre | Propósito
-----|--------
Denegar acceso | Determina si todas las solicitudes se rechazan con una respuesta 403-Prohibido.
Autenticación de token | Determina si se aplicará a una solicitud de autenticación basada en tokens. 
Código de denegación de autorización de token | Determina el tipo de respuesta que se devolverá al usuario cuando se deniega una solicitud debido a la autenticación basada en tokens. 
Ignorar mayúsculas y minúsculas en URL de autenticación de token | Determina si las comparaciones de URL realizadas por la autenticación basada en tokens distinguirán mayúsculas de minúsculas. 
Parámetro de autenticación de token | Determina si debe cambiarse el parámetro de cadena de consulta de autenticación basada en tokens.

### Almacenamiento en caché

Estas características están diseñadas para personalizar cuándo y cómo se almacena el contenido en caché.

Nombre | Propósito
-----|--------
Parámetros de ancho de banda | Determina si se activarán los parámetros de limitación de ancho de banda (es decir, ec\_rate y ec\_prebuf).
Limitación de ancho de banda | Limita el ancho de banda para la respuesta de nuestros servidores perimetrales. 
Omisión de la memoria caché | Determina si la solicitud puede aprovechar nuestra tecnología de almacenamiento en caché.
Tratamiento de encabezado Cache-Control | Controla la generación de encabezados Cache-Control mediante el servidor perimetral cuando está activa la característica Max-Age externa.
Cadena de consulta de clave de caché | Determina si la clave de caché incluirá o excluirá los parámetros de cadena de consulta asociados a una solicitud. 
Reescritura de clave de caché | Reescribe la clave de caché asociada a una solicitud. 
Relleno de la memoria caché completa | Determina lo que ocurre cuando una solicitud tiene como resultado un error de caché parcial en un servidor perimetral. 
Comprimir tipos de archivo | Define los formatos de archivo que se van a comprimir en el servidor. 
Max-Age interna predeterminada | Determina el intervalo predeterminado de max-age para el servidor perimetral en la revalidación de caché del servidor de origen.
Tratamiento del encabezado Expires | Controla la generación de encabezados Expires mediante el servidor perimetral cuando está activa la característica Max-Age externa. 
Max-Age externa | Determina el intervalo de max-age para el explorador en la revalidación de caché del servidor de perimetral. 
Forzar Max-Age interna | Determina el intervalo de max-age para el servidor perimetral en la revalidación de caché del servidor de origen. 
Compatibilidad de H.264 (Descarga progresiva de HTTP) | Determina los tipos de formatos de archivo H.264 que pueden usarse para transmitir contenido en streaming. 
Respetar solicitud de no almacenar en caché | Determina si las solicitudes de no almacenar en caché de un cliente HTTP se reenviarán al servidor de origen.
Ignorar no almacenar en caché de origen | Determina si la CDN omitirá determinadas directivas procedentes de un servidor de origen.
Ignorar intervalos que no se puedan satisfacer | Determina la respuesta que se devolverá a los clientes cuando una solicitud genere un código de estado 416 - No se puede satisfacer el intervalo solicitado. 
Max-Stale interna | Controla cuánto tiempo después de la hora de expiración normal puede atenderse un recurso almacenado en caché desde un servidor perimetral cuando el servidor perimetral no puede volver a validar el recurso almacenado en caché con el servidor de origen.
Uso compartido de caché parcial | Determina si una solicitud puede generar contenido almacenado parcialmente en caché. 
PreValidate contenido en caché | Determina si el contenido almacenado en caché será apto para la revalidación temprana antes de que expire su período de vida. 
Actualizar archivos de caché de cero bytes | Determina cómo nuestros servidores perimetrales controlan la solicitud de un cliente HTTP para un recurso de la caché de 0 bytes.
Establecer códigos de estado almacenables en caché | Define el conjunto de códigos de estado que puede dar lugar a contenido almacenado en caché. 
Entrega de contenido obsoleto en caso de error | Determina si se entregará el contenido almacenado en caché cuando se produzca un error durante la revalidación de caché o al recuperar el contenido solicitado desde el servidor de origen del cliente.
Obsoleto durante revalidación | Mejora el rendimiento al permitir que nuestros servidores perimetrales sirvan un cliente obsoleto al solicitante mientras se lleva a cabo la revalidación. 
Comentario | La característica Comentario permite agregar una nota en una regla. 

### Encabezados

Estas características están diseñadas para agregar, modificar o eliminar encabezados de la solicitud o respuesta.

Nombre | Propósito
-----|--------
Encabezado de respuesta Age | Determina si se incluirá un encabezado de respuesta Age en la respuesta enviada al solicitante. 
Depurar encabezados de respuesta de la caché | Determina si una respuesta puede incluir el encabezado de respuesta X-EC-Debug que proporciona información sobre la directiva de caché para el recurso solicitado.
Modificar encabezado de solicitud de cliente | Sobrescribe, agrega o elimina un encabezado en una solicitud.
Modificar encabezado de respuesta de cliente | Sobrescribe, agrega o elimina un encabezado en una respuesta.
Establecer encabezado personalizado de IP de cliente | Permite que la dirección IP del cliente solicitante se agregue a la solicitud como encabezado de solicitud personalizado. 

### Registros

Estas características están diseñadas para personalizar los datos almacenados en archivos de registro sin procesar.

Nombre | Propósito
-----|--------
Campo de registro personalizado 1 | Determina el formato y el contenido que se asignará al campo de registro personalizado en un archivo de registro sin procesar. 
Cadena de consulta del registro | Determina si una cadena de consulta se almacenará con la dirección URL en los registros de acceso.

### Optimizar

Estas características determinan si una solicitud se someterá a las optimizaciones que proporciona Edge Optimizer.

Nombre | Propósito
-----|--------
Edge Optimizer | Determina si Edge Optimizer puede aplicarse a una solicitud. 
Edge Optimizer – Crear instancias de configuración | Crea o activa una instancia de configuración de Edge Optimizer asociada a un sitio. 
 

### Origen

Estas características están diseñadas para controlar la forma en que la red CDN se comunica con un servidor de origen.

Nombre | Propósito
-----|--------
Número máximo de solicitudes de conexión persistente | Define el número máximo de solicitudes de conexión persistente antes de cerrarse. 
Encabezados de proxy especiales | Define el conjunto de encabezados de solicitud específicos de la red CDN que se reenviará desde un servidor perimetral a un servidor de origen. 

### Especialidad

Estas características ofrecen funcionalidad avanzada que solo deben utilizar los usuarios avanzados.

Nombre | Propósito
-----|--------
Métodos HTTP almacenables en caché | Determina el conjunto de métodos HTTP adicionales que pueden almacenarse en caché en nuestra red.
Tamaño del cuerpo de solicitud almacenable en caché | Define el umbral que determina si una respuesta POST se puede almacenar en caché. 
 

### URL

Estas características permiten reescribir una solicitud o redirigirla a una dirección URL diferente.

Nombre | Propósito
-----|--------
Seguir redireccionamientos | Determina si las solicitudes se pueden redirigir al nombre de host definido en el encabezado Ubicación que devuelve el servidor de origen de un cliente. 
Redirección de URL | Redirige las solicitudes a través del encabezado Ubicación. 
Reescritura de direcciones URL | Reescribe la dirección URL de la solicitud. 

### Firewall de aplicaciones web

La característica Firewall de aplicaciones web determina si Firewall de aplicaciones web examinará una solicitud.

## Consulte también
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)

<!---HONumber=AcomDC_1203_2015-->
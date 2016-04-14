<properties
	pageTitle="Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes"
	description="Se explica cómo el nuevo Servicio de aplicaciones de Azure y sus características afectan a los servicios ya existentes de Azure."
	authors="yochayk"
	writer="yochayk"
	editor="yochayk"
	manager="nirma"
	services="app-service"
	documentationCenter=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="yochayk"/>


# Servicio de aplicaciones de Azure y los servicios de Azure existentes

En este artículo se describen las modificaciones de los servicios de Azure ya existentes como parte del cambio para reunir varios servicios de Azure en el [Servicio de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/), una nueva oferta integrada.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Información general

El [Servicio de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/) es un servicio en la nube nuevo y exclusivo que permite a los desarrolladores crear aplicaciones web y móviles destinadas a cualquier plataforma y dispositivo. Se trata de una solución integral diseñada para simplificar las funciones de codificación repetitivas, integrarse en sistemas Saas y empresariales y automatizar los procesos de negocio a la vez que cumplir sus necesidades de seguridad, confiabilidad y escalabilidad.

El Servicio de aplicaciones reúne los siguientes servicios de Azure ya existentes: [Sitios web](https://azure.microsoft.com/services/websites/), [Servicios móviles](https://azure.microsoft.com/services/mobile-services/) y [Servicios de Biztalk](https://azure.microsoft.com/services/biztalk-services/) en un servicio único combinado que, a su vez, agrega nuevas capacidades de gran eficacia. Además, permite hospedar los siguientes tipos de aplicaciones:

-   Aplicaciones web
-   Aplicaciones móviles
-   Aplicaciones de API
-   Aplicaciones lógicas

En la tabla siguiente se explica cómo se asignan los servicios de Azure existentes al Servicio de aplicaciones y los tipos de aplicaciones disponibles en este.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Servicio de Azure existente</th>
<th align="left", style="width:10%">Servicio de aplicaciones de Azure</th>
<th align="left", style="width:80%">Qué cambia</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sitios web Azure</td>
<td align="left">Aplicaciones web</td>
<td align="left"><li>Para Sitios web Azure, el Servicio de aplicaciones se limita estrictamente a cambiar el nombre Sitios web por Aplicaciones web.
<p><li>Todas las instancias existentes de Sitios web serán ahora Aplicaciones web en el Servicio de aplicaciones.</p>
<p><li>Puede tener acceso a los sitios web ya existentes a través del <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portal de Azure</a>, donde los encontrará bajo <em>Aplicaciones web</em>.</p>
<p><li><em>Plan de hospedaje web</em> es ahora <em>Plan del Servicio de aplicaciones</em>. Un <em>Plan del Servicio de aplicaciones</em> puede hospedar cualquier tipo de Servicio de aplicaciones, como Aplicaciones web, Aplicaciones móviles, Aplicaciones lógicas o Aplicaciones de API.</p>
<p><li>Aplicaciones web del Servicio de aplicaciones de Azure tiene ahora disponibilidad general.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Más información sobre Aplicaciones web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Servicios móviles de Azure</td>
<td align="left">Aplicaciones móviles</td>
<td align="left"><p><li>Servicios móviles sigue estando disponible como servicio independiente con plena compatibilidad.</p>
<p><li>Aplicaciones móviles es un tipo de aplicación del servicio de aplicaciones, que integra toda la funcionalidad de los servicios móviles y mucho más.</p>
<p><li>Es fácil <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrar de Servicios móviles a Aplicaciones móviles</a>.</p>
<p><li>Como parte del Servicio de aplicaciones, Aplicaciones móviles incluye nuevas capacidades además de Servicios móviles, como la integración con sistemas locales y SaaS, espacios de ensayo, trabajos web y opciones de escalado mejoradas, entre otras.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Más información sobre Aplicaciones móviles</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Aplicaciones de API</td>
<td align="left">
<p><li>Aplicaciones de API es un nuevo tipo de aplicación del Servicio de aplicaciones que permite compilar y consumir API en la nube fácilmente.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Más información sobre Aplicaciones de API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Aplicaciones lógicas</td>
<td align="left">
<p><li>Aplicaciones lógicas es un nuevo tipo de aplicación del Servicio de aplicaciones que permite automatizar los procesos de negocio fácilmente.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Más información sobre Aplicaciones lógicas</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Servicios de BizTalk de Azure</td>
<td align="left">Aplicaciones de API de BizTalk</td>
<td align="left">
<li><p>Servicios de Biztalk sigue estando disponible como servicio independiente con plena compatibilidad.</p>
<li><p>Todas las capacidades de Servicios de BizTalk se integran en el Servicio de aplicaciones como aplicaciones API, lo que permite a los usuarios realizar tareas de integración de aplicaciones empresariales y de integración B2B con cualquiera de los tipos de aplicaciones del Servicio de aplicaciones.</p>
<li><p>Ahora, con Aplicaciones lógicas, puede automatizar los procesos de negocio a través de una experiencia de diseño visual para crear flujos de trabajo.</p></td>
</tr>
</tbody>
</table>

Para obtener más información, visite [Documentación del Servicio de aplicaciones](https://azure.microsoft.com/documentation/services/app-service/).

<!---HONumber=AcomDC_0218_2016-->
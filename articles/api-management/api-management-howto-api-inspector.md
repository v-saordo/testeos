<properties 
	pageTitle="Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure" 
	description="Obtenga información acerca de cómo realizar un seguimiento de las llamadas con API Inspector en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure

Administración de API ofrece la herramienta API Inspector para ayudarle con la depuración y la solución de problemas de las API. El API Inspector se puede usar mediante programación y también directamente desde el portal para desarrolladores.

Además de las operaciones de seguimiento, API Inspector también realiza el seguimiento de evaluaciones de [expresiones de directivas](https://msdn.microsoft.com/library/azure/dn910913.aspx). Para obtener una demostración, consulte [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) y avance rápidamente hasta el minuto 21:00.

En esta guía se explica el uso del API Inspector.

>[AZURE.NOTE]Los seguimientos de API Inspector solo se generan y están disponibles para las solicitudes que contiene claves de suscripción que pertenecen a la cuenta de [administrador](api-management-howto-create-groups.md).

## <a name="trace-call"> </a> Uso de API Inspector para realizar el seguimiento de una llamada

Para usar API Inspector, agregue un encabezado de solicitud **ocp-apim-trace: true** a la llamada a la operación y luego descargue e inspeccione el seguimiento con la URL indicada mediante el encabezado de respuesta **ocp-apim-trace-location**. Esto puede realizarse mediante programación y también directamente desde el portal para desarrolladores.

Este tutorial muestra cómo utilizar el API Inspector para realizar un seguimiento de las operaciones mediante la API de calculadora básica que se configura en el tutorial de introducción [Administrar su primera API](api-management-get-started.md). Si no ha realizado ese tutorial, solo tardará unos minutos en importar la API de calculadora básica, o puede usar otra API de su elección, como la Echo API. Cada instancia del servicio Administración de API viene previamente configurada con una API Eco que se puede usar para experimentar con Administración de API y aprender de esta. La Echo API devuelve cualquier entrada que se le envíe. Para usarla, se puede invocar cualquier verbo HTTP, y el valor devuelto será simplemente el que se envíe.



Para comenzar, haga clic en **portal para desarrolladores** en el Portal de Azure clásico para el servicio Administración de API. Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API.

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

![API Management developer portal][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y después en **Calculadora básica**.

![API Eco][api-management-api]

Haga clic en **Inténtelo** para intentar la operación **Agregar dos enteros**.

![Pruébelo][api-management-open-console]

Mantenga los valores predeterminados de los valores de parámetros y seleccione la clave de suscripción para el producto que desee usar desde la lista desplegable **suscription-key**.

En el portal para desarrolladores, el encabezado **Ocp-Apim-Trace** ya está establecido en **true**. Este encabezado configura si se ha generado o no un seguimiento.

![Los métodos Send][api-management-http-get]

Haga clic en **Enviar** para invocar la operación.

![Los métodos Send][api-management-send-results]

En los encabezados de respuesta habrá una entrada **ocp-apim-trace-location** con un valor similar al que aparece en el ejemplo siguiente.

	ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

El seguimiento puede descargarse de la ubicación especificada y revisarse, como se demuestra en el paso siguiente.

## <a name="inspect-trace"> </a>Inspección del seguimiento

Para revisar los valores del seguimiento, descargue el archivo de seguimiento de la URL de **ocp-apim-trace-location**. Se trata de un archivo de texto en formato JSON y contiene entradas similares a las que aparecen en el ejemplo siguiente.

	{
	    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
	    "traceEntries": {
	        "inbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0725926",
	                "data": {
	                    "request": {
	                        "method": "GET",
	                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "Connection",
	                                "value": "Keep-Alive"
	                            },
	                            {
	                                "name": "Host",
	                                "value": "contoso5.azure-api.net"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "mapper",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0726213",
	                "data": {
	                    "configuration": {
	                        "api": {
	                            "from": "/calc",
	                            "to": {
	                                "scheme": "http",
	                                "host": "calcapi.cloudapp.net",
	                                "port": 80,
	                                "path": "/api",
	                                "queryString": "",
	                                "query": {},
	                                "isDefaultPort": true
	                            }
	                        },
	                        "operation": {
	                            "method": "GET",
	                            "uriTemplate": "/add?a={a}&b={b}"
	                        },
	                        "user": {
	                            "id": 1,
	                            "groups": [
	                                "Administrators",
	                                "Developers"
	                            ]
	                        },
	                        "product": {
	                            "id": 1
	                        }
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0727522",
	                "data": {
	                    "message": "Request is being forwarded to the backend service.",
	                    "request": {
	                        "method": "GET",
	                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "X-Forwarded-For",
	                                "value": "33.52.215.35"
	                            }
	                        ]
	                    }
	                }
	            }
	        ],
	        "outbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1960601",
	                "data": {
	                    "response": {
	                        "status": {
	                            "code": 200,
	                            "reason": "OK"
	                        },
	                        "headers": [
	                            {
	                                "name": "Pragma",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Length",
	                                "value": "124"
	                            },
	                            {
	                                "name": "Cache-Control",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Type",
	                                "value": "application/xml; charset=utf-8"
	                            },
	                            {
	                                "name": "Date",
	                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
	                            },
	                            {
	                                "name": "Expires",
	                                "value": "-1"
	                            },
	                            {
	                                "name": "Server",
	                                "value": "Microsoft-IIS/8.5"
	                            },
	                            {
	                                "name": "X-AspNet-Version",
	                                "value": "4.0.30319"
	                            },
	                            {
	                                "name": "X-Powered-By",
	                                "value": "ASP.NET"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1961112",
	                "data": {
	                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1963155",
	                "data": {
	                    "message": "Response body streaming to the caller is complete."
	                }
	            }
	        ]
	    }
	}

## <a name="next-steps"> </a>Pasos siguientes

-	Consulte el resto de temas del tutorial [Introducción a la configuración de API avanzada][].
-	Vea una demostración del seguimiento de expresiones de directivas en [Cloud Cover Episodio 177: Más características de administración de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avance rápidamente hasta el minuto 21:00 para ver la demostración.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance
[Introducción a la configuración de API avanzada]: api-management-get-started-advanced.md
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 

<!---HONumber=AcomDC_1210_2015-->
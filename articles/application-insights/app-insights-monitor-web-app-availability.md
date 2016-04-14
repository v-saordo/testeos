<properties
	pageTitle="Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web | Microsoft Azure"
	description="Configure pruebas web en Application Insights. Obtenga alertas si un sitio web deja de estar disponible o responde con lentitud."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/01/2016"
	ms.author="awills"/>

# Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sito web

Después de haber implementado la aplicación web, puede configurar pruebas web para supervisar su disponibilidad y capacidad de respuesta. Application Insights enviará solicitudes web a intervalos regulares desde puntos de todo el mundo y puede alertarle si la aplicación responde lentamente o no responde en absoluto.

![Ejemplo de prueba web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Puede configurar pruebas web para cualquier punto de conexión HTTP o HTTPS que sea accesible desde la red pública de Internet.

Existen dos tipos de prueba web:

* [Prueba de ping de la dirección URL](#set-up-a-url-ping-test): una prueba sencilla que se puede crear en el portal de Azure.
* [Prueba web de varios pasos](#multi-step-web-tests): que se crea en Visual Studio Ultimate o Visual Studio Enterprise y se carga en el portal.

Puede crear hasta 10 pruebas web por recurso de aplicación.


## Configuración de una prueba de ping de la dirección URL

### <a name="create"></a>1. ¿Crear un nuevo recurso?

Omita este paso si ya ha [configurado un inicio de recurso de Application Insights][start] para esta aplicación y desea ver los datos de disponibilidad en el mismo lugar.

Suscríbase a [Microsoft Azure](http://azure.com), vaya al [portal de Azure](https://portal.azure.com) y cree un nuevo recurso de Application Insights.

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Se abrirá la hoja de información general para el nuevo recurso. Para encontrar esta opción en cualquier momento en el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar**.

### <a name="setup"></a>2. Crear una prueba web

En el recurso de Application Insights, busque el icono de disponibilidad. Haga clic para abrir la hoja de pruebas web para la aplicación y agregue una prueba web.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **La dirección URL** debe ser visible desde la red pública de Internet. Puede incluir una cadena de consulta, así por ejemplo se puede ejercitar un poco la base de datos. Si la dirección URL se resuelve en una redirección, la seguiremos, con un máximo de 10 redirecciones.
- **Analizar solicitudes dependientes**: imágenes, scripts, archivos de estilo y otros recursos de la página se solicitan como parte de la prueba. La prueba da error si todos estos recursos no se pueden descargar correctamente dentro del tiempo de espera de la prueba entera.
- **Habilitar reintentos**: cuando la prueba da error, se reintenta tras un corto intervalo. Se notifica un error únicamente si los tres intentos sucesivos producen un error. Las sucesivas pruebas se realizan según la frecuencia habitual de la prueba. El reintento se suspende temporalmente hasta que uno se complete correctamente. Esta regla se aplica independientemente en cada ubicación de la prueba. (Se recomienda esta configuración. Como media, cerca del 80 % de los errores desaparecen al reintentar).
- **Frecuencia de prueba**: establece la frecuencia con que se ejecuta la prueba desde cada ubicación de prueba. Con una frecuencia de cinco minutos y cinco ubicaciones de prueba, el sitio se prueba cada minuto por término medio.
- Las **ubicaciones de prueba** son los lugares desde donde nuestros servidores envían solicitudes web a la dirección URL. Elija más de una de tal forma que pueda distinguir los problemas del sitio web a partir de los problemas de red. Puede seleccionar hasta 16 ubicaciones.

- **Criterios de éxito**:

    **Tiempo de espera de prueba**: reduzca este valor para recibir una alerta sobre las respuestas lentas. La prueba se considera un error si no se han recibido respuestas de su sitio dentro de este período. Si seleccionó **Analizar solicitudes dependientes**, todas las imágenes, archivos de estilo, scripts y otros recursos dependientes se deben haber recibido durante este período.

    **Respuesta HTTP**: el código de estado devuelto que se considera correcto. 200 es el código que indica que se ha devuelto una página web normal.

    **Coincidencia de contenido**: una cadena, como "Bienvenido". Realizaremos una prueba que tenga lugar en todas las respuestas. Debe ser una cadena sin formato, sin caracteres comodín. No se olvide de que si el contenido cambia, es posible que tenga que actualizarla.


- De forma predeterminada, se le envían **alertas** cuando hay errores en tres ubicaciones durante cinco minutos. Es probable que un error en una ubicación sea un problema de red y no un problema con su sitio. No obstante, puede cambiar el umbral a más o menos sensible, y también puede cambiar las personas a quienes se deben enviar los correos electrónicos.

#### Prueba de más URL

Agregue más pruebas. Por ejemplo, además de probar la página principal, puede asegurarse de que la base de datos se está ejecutando probando la URL con una búsqueda.


### <a name="monitor"></a>3. Ver informes de disponibilidad

Después de uno o dos minutos, haga clic en **Actualizar** en la hoja de pruebas de disponibilidad o web. (No se actualiza automáticamente).

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Haga clic en cualquier barra del gráfico de resumen en la parte superior para obtener una vista más detallada de ese período de tiempo.

Estos gráficos combinan los resultados de todas las pruebas web de esta aplicación.

#### Componentes de la página web

Como parte de la prueba se solicitan imágenes, hojas de estilo, scripts y otros componentes estáticos de la página web que está probando.

El tiempo de respuesta registrado es el tiempo necesario para que se complete la carga de todos los componentes.

Si no se puede cargar alguno de los componentes, la prueba se marca con errores.

## <a name="failures"></a>Si ve errores...

Haga clic en un punto rojo.

![Click a red dot](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

O bien, desplácese hacia abajo y haga clic en una prueba donde vea un éxito de menos del 100%.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

De este modo se muestran los resultados de la prueba.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

La prueba se ejecuta desde varias ubicaciones, elija una donde los resultados sean inferiores al 100 %.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


Desplácese hacia abajo hasta las **pruebas con errores** y elija un resultado.

Haga clic en el resultado para evaluarlo en el portal y ver el motivo del error.

![Webtest run result](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


También puede descargar el archivo de resultados e inspeccionarlo en Visual Studio.


*¿Parece que se ha completado correctamente pero se notifica como un error?* Compruebe todas las imágenes, los scripts, las hojas de estilo y cualquier otro archivo cargado por la página. Si se produce un error en cualquiera de ellos, se notificará que la prueba ha concluido con errores, incluso si la página html principal se carga correctamente.



## Pruebas web de varios pasos

Puede supervisar un escenario que implique una secuencia de direcciones URL. Por ejemplo, si está supervisando un sitio web de ventas, puede probar que la incorporación de elementos al carro de la compra funciona correctamente.

Para crear una prueba de varios pasos, grabe el escenario con Visual Studio y, a continuación, cargue la grabación en Application Insights. Application Insights reproducirá el escenario a intervalos y comprobará las respuestas.

Tenga en cuenta que no puede usar funciones codificadas en las pruebas: los pasos del escenario deben incluirse como un script en el archivo .webtest.

#### 1\. Grabar un escenario

Utilice Visual Studio Enterprise o Ultimate para grabar una sesión web.

1. Cree un proyecto de prueba de rendimiento web.

    ![En Visual Studio, cree un nuevo proyecto a partir de la plantilla de prueba de carga y rendimiento web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Abra el archivo .webtest y empiece a grabar.

    ![Abra el archivo .webtest y haga clic en Grabar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Realice las acciones del usuario que desea simular en la prueba: abra el sitio web, agregue un producto al carro, etc. Después, detenga la prueba.

    ![Se ejecuta la grabadora de la prueba web en Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    No cree un escenario largo. Existe un límite de 100 pasos y 2 minutos.

4. Edite la prueba para:
 - Agregar validaciones que comprueben el texto recibido y los códigos de respuesta.
 - Quite las interacciones que sean superfluas. También puede quitar las solicitudes dependientes relacionadas con imágenes o con sitios de anuncios o de seguimiento.

    Recuerde que solo se puede editar el script de prueba: no se puede agregar código personalizado ni llamar a otras pruebas web. No inserte bucles en la prueba. Puede utilizar complementos de prueba web estándar.

5. Ejecute la prueba en Visual Studio para asegurarse de que funciona.

    El ejecutor de pruebas web abre un explorador web y repite las acciones grabadas. Asegúrese de que funciona como se esperaba.

    ![En Visual Studio, abra el archivo .webtest y haga clic en Ejecutar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2\. Cargar la prueba web en Application Insights

1. En el portal de Application Insights, cree una nueva prueba web.

    ![En la hoja de pruebas web, seleccione Agregar.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Seleccione la prueba de varios pasos y cargue el archivo .webtest.

    ![Seleccione la prueba web de varios pasos.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Establezca las ubicaciones de prueba, la frecuencia y los parámetros de alerta de la misma manera que para las pruebas de ping.

Vea los resultados y los errores de la prueba igual que hace con las pruebas de una sola dirección URL.

Un motivo habitual de error es que la prueba se ejecuta durante demasiado tiempo. No deben ejecutar durante más de dos minutos.

No olvide que todos los recursos de una página se deben cargar correctamente para que la prueba sea correcta, incluidos los scripts, las hojas de estilo, las imágenes, etc.

Tenga en cuenta que la prueba web debe estar contenida totalmente en el archivo .webtest: no puede usar las funciones codificadas en la prueba.


### Conexión de tiempo y números aleatorios a su prueba de varios pasos

Suponga que está probando una herramienta que obtiene datos que dependen del tiempo, como acciones de una fuente externa. Cuando se graba la prueba web, debe utilizar horas específicas, pero las establece como parámetros de la prueba, StartTime y EndTime.

![Una prueba web con parámetros.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Al ejecutar la prueba, le gustaría que EndTime fuera siempre la hora actual y que StartTime fuera 15 minutos menos.

Los complementos de prueba web proporcionan la manera de hacerlo.

1. Agregue un complemento de prueba de web a cada valor variable que desee. En la barra de herramientas de pruebas web, elija **Agregar complemento de prueba web**.

    ![Elija Agregar complemento de prueba web y seleccione un tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    En este ejemplo, vamos a utilizar dos instancias del complemento de tiempo fecha. Es una instancia para "hace 15 minutos" y otra para "ahora".

2. Abra las propiedades de cada complemento. Asígnele un nombre y configúrelo para utilizar la hora actual. En cada uno de ellos, establezca Añadir minutos = -15.

    ![Establezca el nombre, Usar hora actual y Agregar minutos.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. En los parámetros de prueba en la web, utilice {{plug-in name}} para hacer referencia al nombre de un complemento.

    ![En el parámetro de la prueba, utilice {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ahora puede cargar la prueba en el portal. Utilizará los valores dinámicos en cada ejecución de la prueba.

## Tratamiento del inicio de sesión

Si los usuarios inician sesión en la aplicación, tiene varias opciones para simular el inicio de sesión para poder probar páginas detrás del inicio de sesión. El enfoque que utilice dependerá del tipo de seguridad proporcionada por la aplicación.

En todos los casos, debe crear una cuenta solo con fines de prueba. Si es posible, restrinja sus permisos para que sea de solo lectura.

* Nombre de usuario y contraseña simples: solo debe registrar una prueba web de la forma habitual. Elimine las cookies en primer lugar.
* Autenticación SAML. Para ello, puede utilizar el complemento SAML que está disponible para las pruebas web.
* Secreto de cliente: si la aplicación tiene una ruta de inicio de sesión que implica un secreto de cliente, utilícela. Azure Active Directory la proporciona. 
* Autenticación abierta: por ejemplo, al iniciar sesión con su cuenta de Microsoft o Google. Muchas aplicaciones que utilizan OAuth proporcionan la alternativa de secreto de cliente, por lo que es la primera táctica que hay que investigar. Si la prueba tiene que iniciar sesión con OAuth, el enfoque general es el siguiente:
 * Utilice una herramienta como Fiddler para examinar el tráfico entre el explorador web, el sitio de autenticación y la aplicación. 
 * Realice dos o más inicios de sesión mediante distintas máquinas o exploradores o en intervalos largos de tiempo (para permitir que los tokens expiren).
 * Mediante la comparación de diferentes sesiones, identifique el token pasado desde el sitio de autenticación que, a continuación, se pasa al servidor de aplicaciones después de iniciar sesión. 
 * Grabe una prueba web con Visual Studio. 
 * Parametrice los tokens, estableciendo el parámetro cuando se devuelve el token desde el autenticador y utilizándolo en la consulta al sitio. (Visual Studio intentará parametrizar la prueba, pero no parametrizará correctamente los tokens).


## <a name="edit"></a> Modificación o deshabilitación de una prueba

Abra una prueba individual para editarla o deshabilitarla.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Es posible que desee deshabilitar las pruebas web mientras está realizando un mantenimiento en el servicio.

## Automatización

También puede [usar scripts de PowerShell para configurar una prueba web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/) automáticamente.

## ¿Tiene preguntas? ¿Tiene problemas?

* *¿Puedo llamar el código desde mi prueba web?*

    No. Los pasos de la prueba deben encontrarse en el archivo .webtest. Y no se puede llamar a otras pruebas web ni utilizar bucles. Pero hay varios complementos que pueden resultarle útiles.

* *¿Se admite HTTPS?*

    Actualmente, se admiten SSL 3.0 y TLS 1.0.

* *¿Existe alguna diferencia entre las "pruebas web" y las "pruebas de disponibilidad"?*

    Usamos ambos términos de manera intercambiable.

* *Me gustaría usar pruebas de disponibilidad en nuestro servidor interno que se ejecuta detrás de un firewall.*

    Configure el firewall para que permita las solicitudes de las direcciones IP de la lista que aparece al final de este artículo.

## <a name="video"></a>Vídeo

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Pasos siguientes

[Búsqueda de registros de diagnóstico][diagnostic]

[Solución de problemas][qna]


## Direcciones IP de pruebas web

Si necesita abrir un firewall para que se puedan realizar las pruebas web, esta es la lista actual de direcciones IP. Dicha lista puede cambiar de vez en cuando.

Abra los puertos 80 (http) y 443 (https).

```

213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
65.55.244.40
65.55.244.17
65.55.244.42
65.55.244.37
65.55.244.15
65.55.244.16
65.55.244.44
65.55.244.18
65.55.244.46
65.55.244.47
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.67
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48

```


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0309_2016-->
<properties 
	pageTitle="Supervisión de dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java" 
	description="Supervisión extendida de sitios web de Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>
 
# Supervisión de dependencias, excepciones y tiempos de ejecución en aplicaciones web de Java

*Application Insights se encuentra en su versión de vista previa.*

Si ha [instrumentado la aplicación web de Java con Application Insights][java], puede usar el agente de Java para obtener información más clara, sin tener que realizar cambios de código:


* **Dependencias:** datos sobre las llamadas realizadas por la aplicación a otros componentes, por ejemplo:
 * **Llamadas REST** realizadas a través de HttpClient, OkHttp y RestTemplate (Spring).
 * **Redis** llamadas realizadas a través del cliente de Jedis. Si la llamada tarda más de 10 s, el agente capturará también los argumentos de la llamada.
 * **[Llamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**: MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB o DB Derby de Apache. Se admiten llamadas "executeBatch". En el caso de MySQL y PostgreSQL, si la llamada tarda más de 10 s, el agente notificará el plan de consulta. 
* **Excepciones detectadas:** datos sobre las excepciones que controla el código.
* **Tiempo de ejecución del método:** datos sobre el tiempo necesario para ejecutar métodos específicos.

Para usar el agente de Java, debe instalarlo en el servidor. Las aplicaciones web deben instrumentarse con el [SDK de Application Insights para Java][java].

## Instalación del agente de Application Insights para Java

1. [Descargue el agente](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html) en el equipo que ejecuta el servidor de Java.
2. Edite el script de inicio del servidor de aplicaciones y agregue la siguiente JVM:

    `javaagent:`*ruta de acceso completa al archivo JAR del agente*

    Por ejemplo, en Tomcat en un equipo Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie el servidor de aplicaciones.

## Configuración del agente

Cree un archivo denominado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR del agente.

Establezca el contenido del archivo XML. Edite el ejemplo siguiente para incluir u omitir las características que desee.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments will be sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan will be reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Debe habilitar la excepción de los informes y los intervalos de método para métodos individuales.

De forma predeterminada, `reportExecutionTime` es true y `reportCaughtExceptions` es false.

## Visualización de los datos

En el recurso de Application Insights, aparecerán tiempos de ejecución agregados de métodos y dependencias remotos [en el icono Rendimiento][metrics].

Para buscar instancias individuales de informes de dependencia, excepción y método, abra [Buscar][diagnostic].

[Más información sobre diagnósticos de problemas de dependencia](app-insights-dependencies.md#diagnosis).



## ¿Tiene preguntas? ¿Tiene problemas?

[Solución de problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0302_2016-->
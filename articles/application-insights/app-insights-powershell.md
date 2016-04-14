<properties 
	pageTitle="Creación de recursos de Application Insights mediante PowerShell" 
	description="Cree recursos de Application Insights mediante programación como parte de la compilación." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/22/2016" 
	ms.author="awills"/>
 
# Creación de recursos de Application Insights mediante PowerShell

En este artículo se muestra cómo crear un recurso de [Application Insights](app-insights-overview.md) en Azure automáticamente. Puede hacerlo, por ejemplo, como parte de un proceso de compilación. Junto con el recurso básico de Application Insights, puede crear [pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md), [configurar alertas](app-insights-alerts.md) y crear otros recursos de Azure.

La clave para crear estos recursos es las plantillas JSON para el [Administrador de recursos de Azure](../powershell-azure-resource-manager.md). En pocas palabras, el procedimiento es: descargar las definiciones JSON de los recursos existentes; parametrizar determinados valores como los nombres; y luego ejecutar la plantilla siempre que se quiera crear un nuevo recurso. Puede empaquetar varios recursos juntos para crearlos todos en un solo paso, por ejemplo, un monitor de aplicaciones con pruebas de disponibilidad, alertas y almacenamiento para la exportación continua. Existen algunos matices a algunas de las parametrizaciones automáticas, que se explican aquí.

## Instalación única

Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en la máquina donde quiere ejecutar los scripts.

1. Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Úselo para instalar Microsoft Azure Powershell.

## Copia de JSON para los recursos existentes

1. Configure [Application Insights](app-insights-overview.md) para un proyecto similar a los que quiere generar automáticamente. Si lo desea, agregue pruebas web y alertas.
2. Cree un nuevo archivo .json. Vamos a llamarlo `template1.json` en este ejemplo. Copie este contenido en él:


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
			"testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    Esta plantilla configurará una prueba de disponibilidad además del recurso principal.


2. Abra el [Administrador de recursos de Azure](https://resources.azure.com/). Desplácese por las suscripciones, los grupos de recursos y los componentes hasta su recurso de aplicación.

    ![](./media/app-insights-powershell/01.png)

    *Componentes* son los recursos básicos de Application Insights para mostrar aplicaciones. Existen recursos distintos para las reglas de alerta asociadas y las pruebas web de disponibilidad.

3. Copie el código JSON del componente en el lugar adecuado en `template1.json`.
6. Elimine estas propiedades:
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. Abra las secciones de pruebas web y reglas de alertas y copie el código JSON para los elementos individuales de la plantilla. (No copie de los nodos de pruebas web o reglas de alerta: vaya a los elementos que hay debajo de ellos).

    Cada prueba web tiene una regla de alerta asociada, por lo que tiene que copiar las dos.

    La prueba web debe ir antes que la regla de alerta.

5. Para satisfacer el esquema, inserte esta línea en cada recurso:

    `"apiVersion": "2014-04-01",`

    (El esquema también reclama el uso de mayúsculas en los nombres de tipo de recurso `Microsoft.Insights/*`, pero *no* los cambie).


## Parametrización de la plantilla

Ahora, debe reemplazar los nombres específicos por parámetros. Para [parametrizar una plantilla](../resource-group-authoring-templates.md), escriba expresiones mediante un [conjunto de funciones auxiliares](../resource-group-template-functions.md).

No se puede parametrizar solo una parte de una cadena, así que use `concat()` para compilar las cadenas.

Éstos son ejemplos de sustituciones que querrá realizar. Hay varias repeticiones de cada sustitución. Y puede que tenga otras en la plantilla. En estos ejemplos se usan los parámetros y las variables que se han definido en la parte superior de la plantilla.

find | reemplazar por
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"` (minúscula) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"`|`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" `

## Si la aplicación es una aplicación web de Azure

Agregue este recurso, o si ya existe ahí un recurso `siteextensions`, parametrícelo de este modo:

```json
    {
      "apiVersion": "2014-04-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

Este recurso implementa el SDK de Application Insights en la aplicación web de Azure.

## Establecimiento de dependencias entre los recursos

Azure debe instalar los recursos en un orden estricto. Para tener la seguridad de que una instalación finaliza antes de que comience la siguiente, agregue líneas de dependencia:

* En el recurso de prueba web:

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* En el recurso de alerta:

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Creación de recursos de Application Insights

1. En PowerShell, inicie sesión en Azure

    `Login-AzureRmAccount`

2. Ejecute un comando similar al siguiente:

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName es el grupo donde desea crear los nuevos recursos.
    * -templateFile debe aparecer antes que los parámetros personalizados.
    * -appName es el nombre del recurso que se va a crear.
    * -webTestName es el nombre de la prueba web para crear.
    * -Url es la dirección URL de la aplicación web.
    * -text es una cadena que aparece en la página web.
    * -siteName: se utiliza si es un sitio web de Azure


## Definición de alertas de métrica

Existe un [método de PowerShell de configuración de alertas](app-insights-alerts.md/#set-alerts-by-using-powershell).


## los cmdlets

A continuación se muestra el componente completo, la prueba web y la plantilla de alerta de prueba web que he creado:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## Consulte también

Otros artículos de automatización:

* [Script de PowerShell para crear un recurso de Application Insights](app-insights-powershell-script-create-resource.md): método rápido sin necesidad de plantilla.
* [Uso de PowerShell para configurar alertas en Application Insights](app-insights-powershell-alerts.md)
* [Envío de Azure Diagnostics a Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Creación de anotaciones de versión](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

<!---HONumber=AcomDC_0211_2016-->
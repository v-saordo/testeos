### Plan del Servicio de aplicaciones

Crea el plan de servicio para hospedar la aplicación web. Debe proporcionar el nombre del plan a través del parámetro **hostingPlanName**. La ubicación del plan es la misma ubicación que se usa para la aplicación web. El tamaño de trabajo y de nivel de precios se especifican en los parámetros **sku** y **workerSize**

    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "numberOfWorkers": 1
      }
    },

<!---HONumber=AcomDC_1203_2015-->
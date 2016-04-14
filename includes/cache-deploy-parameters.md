
### redisCacheName

El nombre de la Caché en Redis de Azure que se creará.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

El nivel de precios de la nueva Caché en Redis de Azure.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Standard"
    }

La plantilla define los valores permitidos para este parámetro (Básico o Estándar) y asigna un valor predeterminado (Estándar) si no se especifica ningún valor. Básico ofrece un único nodo con varios tamaños disponibles hasta 53 GB. Estándar proporciona dos nodos, principal/réplica, con varios tamaños disponibles de hasta 53 GB y un contrato de nivel de servicio del 99,9%.

### redisCacheFamily

La familia de la SKU.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

El tamaño de la nueva instancia de Caché en Redis de Azure.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 1
    }

La plantilla define los valores permitidos para este parámetro (0, 1, 2, 3, 4, 5 o 6) y asigna un valor predeterminado (1) si no se especifica ningún valor. Los números corresponden a los siguientes tamaños de caché: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

### redisCacheVersion

La versión del servidor Redis de la nueva caché.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=Oct15_HO3-->
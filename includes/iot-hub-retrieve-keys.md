## Recuperar las claves del Centro de IoT

Mostrar las claves de autenticación del nuevo Centro de IoT.

1. Agregue el método siguiente a Program.cs:

    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
    
    }
    ```

2. Agregue el código siguiente al método **ShowIoTHubKeys** para imprimir las claves de autenticación en la consola:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
    
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->
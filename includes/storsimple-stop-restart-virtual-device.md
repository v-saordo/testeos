#### Para detener e iniciar un dispositivo virtual
Para detener un dispositivo virtual, haga clic en su nombre y, a continuación, haga clic en **Apagar**. Mientras se está cerrando el dispositivo virtual, su estado es **Deteniéndose**. Una vez detenido el dispositivo virtual, su estado es **Detenido**.

Use los siguientes cmdlets para detener e iniciar un dispositivo virtual.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### Para reiniciar un dispositivo virtual

Cuando se ejecuta un dispositivo virtual y desea reiniciarlo, haga clic en su nombre y, a continuación, haga clic en **Reiniciar**. Mientras se reinicia el dispositivo virtual, su estado es **Reiniciando**. Cuando el dispositivo virtual está listo para su uso, su estado es **En ejecución**.

Use el siguiente cmdlet para reiniciar un dispositivo virtual.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->
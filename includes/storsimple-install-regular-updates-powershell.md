<!--author=SharS last changed: 9/17/15-->

#### Para instalar actualizaciones regulares a través de Windows PowerShell para StorSimple

1. Abra la consola serie del dispositivo y seleccione la opción 1, **Iniciar sesión con acceso completo**. Escriba la contraseña. La contraseña predeterminada es *Password1*. 

2. En el símbolo del sistema, escriba:

     `Get-HcsUpdateAvailability`
    
    Se le notificará si hay actualizaciones disponibles y si son problemáticas.

3. En el símbolo del sistema, escriba:

     `Start-HcsUpdate`

    Dará comienzo el proceso de actualización.

> [AZURE.IMPORTANT]
>
> - Este comando solo se aplica a las actualizaciones regulares. Este comando se ejecuta en un solo controlador, pero se actualizarán ambos controladores. 
> - Es posible que observe una conmutación por error del controlador durante el proceso de actualización. Sin embargo, la conmutación por error no afectará a la disponibilidad o el funcionamiento del sistema.

<!---HONumber=Oct15_HO3-->
<!--author=SharS last changed: 11/16/15-->

#### Para instalar la Actualización 1.2 desde Windows PowerShell para StorSimple

1. Realice los pasos siguientes para descargar la actualización de software.

    1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Si es la primera vez que es usuario de esta página, se le pedirá que instale un catálogo de Microsoft Update. Haga clic en **Instalar**.
    
        ![Instalación del catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Verá una pantalla de búsqueda de catálogo. Escriba **3063418** en el cuadro de búsqueda y haga clic en **Buscar**.

        ![Búsqueda de catálogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Verá la agrupación de **StorSimple Update 1.2 Appliance Update**. Haga clic en **Agregar**. La actualización se agregará a la cesta.

        ![Actualización de agrupación](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Haga clic en **Ver cesta**.
 
        ![Visualización de cesta](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezca la descarga. Se descargará la actualización en una carpeta **StorSimple Update 1.2 Appliance Update bundle** (KB3063418) en la ubicación elegida. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.
    
	En este procedimiento se describe cómo instalar la actualización del dispositivo de software como una revisión, las actualizaciones de firmware de disco desde el servidor de Microsoft Update y el controlador de LSI y las actualizaciones de Windows desde el Portal de Azure clásico. Sin embargo, podría elegir instalar las actualizaciones de software, controladores y firmware de disco como revisiones. Luego necesitará descargar la actualización del controlador de StorSimple 1.2 SAS (KB3043005) y la actualización de firmware de disco de StorSimple 1.2 (KB3063416) y copiar en la misma carpeta compartida. Para instalar las actualizaciones de firmware de disco como revisión, siga las instrucciones de [instalar revisiones del modo de mantenimiento a través de Windows PowerShell para StorSimple](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple).
    
	> [AZURE.NOTE]La revisión debe ser accesible desde ambos controladores para detectar los posibles mensajes de error desde el controlador del mismo nivel.
            
2. Para instalar la actualización de software, acceda a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Siga las instrucciones detalladas en [Uso de PuTTy para conectarse a la consola serie del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

3. En el símbolo del sistema, presione **Entrar**.

4. Seleccione **Opción 1** para iniciar sesión en el dispositivo con acceso completo.

5. Para instalar el paquete de actualización, en el símbolo del sistema, escriba:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Use IP en lugar de DNS en la ruta de acceso de recurso compartido en el comando anterior. El parámetro credential se usa únicamente si tiene acceso a un recurso compartido autenticado.

	Se recomienda que use el parámetro de credencial para obtener acceso a los recursos compartidos. Incluso los recursos compartidos que están abiertos para "todos" no suelen estar abiertos a los usuarios no autenticados.

    A continuación se muestra una salida de ejemplo.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Escriba **Y** cuando se le solicite que confirme la instalación de la revisión.

7. Supervise la actualización mediante el cmdlet `Get-HcsUpdateStatus`.

    La siguiente salida de ejemplo muestra la actualización en curso. `RunInprogress` será `True` cuando la actualización esté en curso.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     La siguiente salida de ejemplo indica que ha finalizado la actualización. `RunInProgress` será `False` cuando se haya completado la actualización.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]En ocasiones, el cmdlet notifica `False` cuando la actualización está todavía en curso. Para garantizar que la revisión está completada, espere unos minutos, vuelva a ejecutar este comando y compruebe que `RunInProgress` es `False`. Si es así, se habrá completado la revisión.
	
8. Cuando se complete la actualización del software, compruebe las versiones de software del sistema. Escriba el siguiente comando:

    `Get-HcsSystem`

    Verá las versiones siguientes:

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433 
    
	Si los números de versión no cambian después de aplicar la actualización, indica que la revisión no se ha podido aplicar. Si ve esto, póngase en contacto con [Soporte de Microsoft](storsimple-contact-microsoft-support.md) para obtener más ayuda.
    
9. Ahora instalará las actualizaciones de firmware de disco que son perjudiciales y tardan de 30 a 45 minutos en completarse. Puede elegir instalarlas en una ventana de mantenimiento planificado mediante la conexión a la consola serie del dispositivo. Para instalar las actualizaciones de firmware de disco, siga las instrucciones que encontrará en [Instalar las actualizaciones del modo de mantenimiento a través de Windows PowerShell para StorSimple](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

10. Cuando las actualizaciones de firmware de disco se apliquen correctamente y el dispositivo haya salido del modo de mantenimiento, regrese al Portal de Azure clásico. Las actualizaciones del modo de mantenimiento no se actualizan en el portal hasta que no han transcurrido 24 horas. Es posible que deba esperar para aplicar las actualizaciones restantes sin interrupciones desde el Portal de Azure clásico.

11. Cuando esté listo para aplicar actualizaciones, vaya a la página **Mantenimiento** y en la parte inferior de la página, haga clic en **Examinar actualizaciones**. Se le notificará que las actualizaciones están disponibles; estas incluyen el controlador y las actualizaciones de Windows. Haga clic en **Instalar actualizaciones** para comenzar el proceso de instalación. Habrá terminado correctamente cuando se instalen correctamente todas las actualizaciones.





 
 

<!---HONumber=AcomDC_1203_2015-->
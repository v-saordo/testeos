
1. Asegúrese de detener el servicio móvil si actualmente se está ejecutando en IIS Express. Haga clic con el botón secundario en el icono de bandeja de IIS Express y, a continuación, haga clic en la opción de **detener** el servicio móvil.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. En una ventana de símbolo del sistema, ejecute el comando **ipconfig** para buscar una dirección IP local válida para la estación de trabajo.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. En Visual Studio, abra el archivo applicationhost.config para IIS Express. Este archivo se encuentra en el siguiente subdirectorio del directorio del perfil de usuario.

        C:\Users<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Configure IIS Express para permitir las solicitudes de conexión remota al servicio. Para ello, en el archivo applicationhost.config, busque el elemento de sitio correspondiente al servicio móvil y agregue un nuevo elemento `binding` para el puerto utilizando la dirección IP que anotó anteriormente. A continuación, guarde el archivo applicationhost.config.

    El elemento de sitio actualizado debería tener una apariencia similar a esta:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Abra la consola del Firewall de Windows y cree una nueva regla de puerto para permitir las conexiones al puerto. Para obtener más información acerca de cómo crear una nueva regla de puerto del Firewall de Windows, consulte [Incorporación de una regla de puerto en un equipo local].

    >[AZURE.NOTE]Si la máquina de pruebas está asociada a un dominio, es posible que las excepciones de firewall estén controladas por una directiva de dominio. En este caso, tendría que ponerse en contacto con el administrador del dominio para obtener una exención para el puerto en su máquina.

    Ahora debería estar listo para realizar pruebas del hospedaje del servicio móvil por parte de IIS Express.

    >[AZURE.NOTE]Cuando acabe de probar el servicio localmente, debe eliminar la regla del Firewall de Windows que creó.


<!-- URLs. -->
[Incorporación de una regla de puerto en un equipo local]: http://go.microsoft.com/fwlink/?LinkId=392240

<!---HONumber=Oct15_HO3-->
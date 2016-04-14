Aunque es posible pegar un URI de MongoLAB en el código, recomendamos configurarlo en el entorno para obtener una mayor facilidad en la administración. De esta forma, si el URI cambia, puede actualizarlo a través del Portal de Azure sin dirigirse al código.


1. En el Portal de Azure, seleccione **Aplicaciones web**.
1. Haga clic en el nombre de la aplicación web en la lista de aplicaciones web. ![WebAppEntry][entry-website] Aparecerá el panel de la aplicación web.

1. Haga clic en **Configurar** en la barra de menús. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Desplácese hacia abajo hasta la sección Cadenas de conexión. ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Para **Nombre**, escriba MONGOLAB\_URI.
1. Para **Value**, pegue la cadena de conexión que se obtuvo en la sección anterior.
1. Seleccione **Personalizado** en la lista desplegable **Tipo** (en lugar del valor predeterminado **SQLAzure**).
1. Haga clic en **Guardar** en la barra de herramientas. ![SaveWebApp][button-website-save]

**Nota:** Azure agrega el prefijo **CUSTOMCONNSTR\_** a esta variable, y este es el motivo por el que el código anterior hace referencia a **CUSTOMCONNSTR\_MONGOLAB\_URI**.

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=Oct15_HO3-->
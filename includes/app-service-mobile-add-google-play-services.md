1. Abra el Administrador de SDK de Android haciendo clic en el icono de la barra de herramientas de Android Studio o haciendo clic en **Herramientas** -> **Android** -> **Administrador de SDK** en el menú. Busque la versión de destino del SDK de Android que se utiliza en el proyecto, ábralo y elija **API de Google**, en caso de que no se haya instalado todavía.

2. Vaya a **Archivo**, **Estructura de proyecto**. A continuación, habilite las notificaciones push en **Notificaciones**.

3. Abra **AndroidManifest.xml** y agregue esta etiqueta a la etiqueta *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->
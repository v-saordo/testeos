1. Abra el Administrador de SDK de Android haciendo clic en el icono de la barra de herramientas de Android Studio o haciendo clic en **Herramientas** -> **Android** -> **Administrador de SDK** en el menú. Busque la versión de destino del SDK de Android que se utiliza en el proyecto, ábralo y elija **API de Google**, en caso de que no se haya instalado todavía.

2. Desplácese hacia abajo hasta **Extras**, expándalo y seleccione **Google Play Services**, como se muestra a continuación. Haga clic en **Install Packages**. Tome nota de la ruta de acceso del SDK para usarla en el paso siguiente.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Abra el archivo **build.gradle** en el directorio de aplicaciones.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Agregue esta línea en *dependencias*:

   		compile 'com.google.android.gms:play-services-gcm:8.4.0'

5. En *defaultConfig*, cambie *minSdkVersion* a 9.
 
6. Haga clic en el botón **Sincronizar proyecto con archivos de Gradle** en la barra de herramientas.

7. Abra **AndroidManifest.xml** y agregue esta etiqueta a la etiqueta *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_0204_2016-->
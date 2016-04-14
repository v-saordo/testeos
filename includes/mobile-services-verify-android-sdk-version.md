Debido al desarrollo en curso, puede que la versión del SDK de Android instalada no coincida con la versión del código. El SDK de Android al que se hace referencia en este tutorial es la versión 21, la última en el momento de la escritura. El número de versión puede aumentar a medida que aparezcan nuevas versiones del SDK, de modo que se recomienda usar la última versión disponible.

Dos síntomas de error de coincidencia de la versión son los siguientes:

1. Al compilar o volver a compilar el proyecto, puede obtener mensajes de error de Gradle como "**no se encontró Google Inc.:Google APIs:n de destino**".

2. Los objetos de Android estándar del código que deben resolverse según las instrucciones `import` pueden generar mensajes de error.

Si aparece cualquiera de estos mensajes, es posible que la versión del SDK de Android instalada en Android Studio no coincida con el destino del SDK del proyecto descargado. Para comprobar la versión, realice los siguientes cambios:


1. En Android Studio, haga clic en **Herramientas** = > **Android** = > **Administrador de SDK**. Si no ha instalado la última versión de la plataforma de SDK, haga clic para instalarla. Anote el número de versión.

2. En la pestaña Explorador de proyectos, en **Scripts de Gradle**, abra el archivo **build.gradle (modeule: app)**. Asegúrese de que **compileSdkVersion** y **buildToolsVersion** se establecen en la versión más reciente del SDK instalada. Es posible que las etiquetas tengan este aspecto:
 
	 	    compileSdkVersion 'Google Inc.:Google APIs:21'
    		buildToolsVersion "21.1.2"
	
3. En el Explorador de proyectos de Android Studio, haga clic con el botón secundario en el nodo del proyecto, elija **Propiedades** y en la columna izquierda, seleccione **Android**. Asegúrese de que **Destino de compilación de proyecto** se haya establecido en la misma versión de SDK que **targetSdkVersion**.

4. En Android Studio, el archivo de manifiesto ya no se utiliza para especificar el SDK de destino y la versión del SDK mínima, al contrario que ocurre con Eclipse.

<!---HONumber=Oct15_HO3-->

###<a name="update-app"></a>Actualización de la aplicación para llamar a la API personalizada

1. Agregaremos un botón llamado "Complete All" al lado del botón existente y moveremos ambos botones una línea hacia abajo. En Eclipse, abra el archivo *res\\layout\\activity\_to\_do.xml* en su proyecto de inicio rápido, busque el elemento **LinearLayout** que contiene el elemento **Button** llamado `buttonAddToDo`. Copie **LinearLayout** y péguelo inmediatamente después del original. Elimine el elemento **Button** del primer **LinearLayout**.

2. En el segundo **LinearLayout**, elimine el elemento **EditText** y agregue el siguiente código inmediatamente después del elemento **Button** existente:

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

	Esto agrega un botón nuevo a la página, en una línea aparte, al lado del botón existente.

3. El segundo **LinearLayout** ahora se ve así:

	     <LinearLayout
	        android:layout_width="match_parent" 
	        android:layout_height="wrap_content" 
	        android:background="#71BCFA"
	        android:padding="6dip"  >
	        <Button
	            android:id="@+id/buttonAddToDo"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="addItem"
	            android:text="@string/add_button_text" />
	        <Button
	            android:id="@+id/buttonCompleteItem"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="completeItem"
	            android:text="@string/complete_button_text" />
	    </LinearLayout>
	

4. Abra el archivo res\\values\\string.xml y agregue la siguiente línea de código:

    	<string name="complete_button_text">Complete All</string>



5. En el Explorador de proyectos, haga clic con el botón secundario en el nombre del proyecto de la carpeta *src* (`com.example.{your projects name}`), elija **Nueva** y, a continuación, seleccione **Clase**. En el cuadro de diálogo, escriba **MarkAllResult** en el campo de nombre de clase, seleccione OK y reemplace la definición de clase resultante por el siguiente código:

		import com.google.gson.annotations.SerializedName;
		
		public class MarkAllResult {
		    @SerializedName("count")
		    public int mCount;
		    
		    public int getCount() {
		        return mCount;
			}
		
			public void setCount(int count) {
			        this.mCount = count;
			}
		}

	Esta clase se usa para mantener el valor de recuento de filas que devuelve la API personalizada.

6. Busque el método **refreshItemsFromTable** en el archivo **ToDoActivity.java** y asegúrese de que la primera línea de código en el bloque `try` tiene el siguiente aspecto:

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

	Esto filtra los elementos para que la consulta no devuelva los elementos completados.

7. Asegúrese de que **ToDoActivity.java** contiene las siguientes importaciones al principio del archivo:

		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;

8. En el archivo **ToDoActivity.java**, agregue el siguiente método:

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
	    	
	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();	
	    		}
	    	});
	    }
	
	Este método controla el evento **Click** del botón nuevo. El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores.

### Prueba de la aplicación

1. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar el proyecto en el emulador de Android o en un dispositivo Android conectado.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.


2. En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Add**.

3. Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4. Haga clic en el botón **Completar todo**.

  	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

	Aparece un cuadro de diálogo de mensaje que indica el número de elementos marcados como completos y la consulta filtrada se vuelve a ejecutar, con lo que se borran todos los elementos de la lista.

<!---HONumber=Oct15_HO3-->
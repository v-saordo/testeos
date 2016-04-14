<properties
	pageTitle="Trabajo con la biblioteca de cliente Android de Servicios móviles"
	description="Obtenga información acerca de cómo usar un cliente de Android para Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Uso de la biblioteca de cliente Android para Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Esta guía le indica cómo enfrentarse a determinadas situaciones habituales usando un cliente Android para Servicios móviles de Azure. Entre las situaciones tratadas se encuentran la consulta, la inserción, la actualización y la eliminación de los datos, la autenticación de los usuarios, la administración de los errores y la personalización del cliente.

Si no tiene experiencia en el uso de Servicios móviles, complete primero el tutorial de inicio rápido [Introducción a Servicios móviles]. Completar ese tutorial garantiza que habrá instalado Android Studio; le ayudará a configurar su cuenta y crear su primer servicio móvil para instalar el SDK de servicios móviles, que admite la versión Android 2.2 o posteriores, pero se recomienda usar la versión de Android 4.2 o posteriores.

Puede encontrar la referencia de API de Javadocs para la biblioteca de cliente de Android [aquí](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuración y requisitos previos

Se asume que ha creado un servicio móvil y una tabla. Para obtener más información, consulte [Crear una tabla](http://go.microsoft.com/fwlink/p/?LinkId=298592). En el código usado en este tema, se supone que el nombre de la tabla es *ToDoItem* y que dispondrá de las siguientes columnas:

- id
- text
- complete

A continuación verá el objeto del cliente con el tipo correspondiente:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Cuando se habilita el esquema dinámico, los Servicios móviles de Azure generan automáticamente nuevas columnas en función del objeto de la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Creación del cliente de Servicios móviles
El código siguiente crea el objeto [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) que se usa para obtener acceso al servicio móvil. El código está en el método `onCreate` de la clase de actividad especificada en *AndroidManifest.xml* como una acción **PRINCIPAL** y una categoría **INICIADOR**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

En el código anterior, reemplace `MobileServiceUrl` y `AppKey` por la URL y la clave de aplicación del servicio móvil, en ese orden. Estos datos están disponibles en el Portal de Azure clásico. si selecciona el servicio móvil y hace clic en *Panel*.

##<a name="instantiating"></a>Creación de una referencia de tabla

La forma más sencilla de consultar o modificar los datos de un servicio móvil es utilizar el *modelo de programación con tipo*, ya que Java es un lenguaje con una fuerte presencia de tipos (más adelante trataremos el modelo *sin tipo*). Este modelo ofrece una perfecta serialización y deserialización para JSON usando la biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) al enviar datos entre el cliente y el servicio móvil: el desarrollador no tiene que hacer nada, el marco se encarga de todo.

Lo primero que debe hacer para consultar o modificar los datos es crear un objeto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) llamando al método **getTable** en [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Abordaremos dos sobrecargas de este método:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

En el código siguiente, *mClient* es una referencia al cliente de su servicio móvil.

La [primera sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296839) se usa cuando el nombre de la clase y el de la tabla son iguales:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


La [segunda sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296840) se usa cuando el nombre de la tabla es diferente del nombre del tipo.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>La estructura de API

Desde la versión 2.0 de la biblioteca de cliente, las operaciones de tabla de servicios móviles usan los objetos [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) y [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) en todas las operaciones asincrónicas, como los métodos que implican consultas y operaciones como inserciones, actualizaciones y eliminaciones. Esto facilita realizar varias operaciones (mientras se encuentra en un subproceso en segundo plano) sin tener que tratar con varias devoluciones de llamada anidadas.


##<a name="querying"></a>Consulta de datos desde un servicio móvil

En esta sección se describe cómo generar consultas al servicio móvil. Los subapartados describen distintos aspectos, como la ordenación, el filtrado o la paginación. Finalmente, trataremos cómo puede concatenar estas operaciones.

### <a name="showAll"></a>Devolver todos los elementos de una tabla

El código siguiente devuelve todos los elementos de la tabla *ToDoItem*. Los muestra en la interfaz de usuario agregando los elementos a un adaptador. Este código es similar a lo que está en el tutorial de inicio rápido [Introducción a Servicios móviles].

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


Las consultas como esta usan el objeto [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

La variable *result* devuelve el conjunto de resultados de la consulta y el código que sigue la instrucción `mToDoTable.execute().get()` indica cómo mostrar las filas individuales.


### <a name="filtering"></a>Filtro de datos devueltos

El código siguiente devuelve todos los elementos de la tabla *ToDoItem* cuyo campo *complete* es igual a *false*. *mToDoTable* es la referencia a la tabla de servicio móvil que hemos creado previamente.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Inicie un filtro con una llamada de método [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) en la referencia de tabla. A esto le sigue una llamada de método [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) seguida de una llamada de método que especifica el predicado lógico. Entre los posibles métodos de predicado se incluyen [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) entre otros.

Esto es suficiente para comparar los campos de número y cadena con los valores específicos. No obstante, se puede hacer mucho más.

Por ejemplo, puede filtrar por fechas. Puede comparar todo el campo de fecha, pero también puede comparar partes de las fechas con métodos como [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) y [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). El código parcial siguiente agrega un filtro para los elementos cuyo valor *due date* sea igual a 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

Puede hacer una amplia variedad de filtros complejos en campos de cadena con métodos como [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) y [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). El código parcial siguiente filtra las filas de tabla donde la columna *text* empieza por "PRI0".

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Los campos de número también permiten una amplia variedad de filtros más complejos con métodos como [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) y [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). El código parcial siguiente filtra las filas de tabla cuya columna *duration* empieza por un número par.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


Puede combinar predicados con métodos como [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) y [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Este código parcial combina dos de los ejemplos anteriores.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

Además, también puede agrupar y anidar operadores lógicos, como se indica en este código parcial:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Para obtener más información y ver ejemplos de filtrado, consulte [Exploración de la riqueza del modelo de consulta del cliente Android de Servicios móviles](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (en inglés).

### <a name="sorting"></a>Clasificación de datos devueltos

El código siguiente devuelve todos los elementos de una tabla de *ToDoItems* cuyo campo *text* sigue un orden ascendente. *mToDoTable* es la referencia a la tabla del servicio móvil que ha creado anteriormente.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

El primer parámetro del método [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) es una cadena igual al nombre del campo por el que realizar la ordenación.

El segundo parámetro usa la enumeración [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) para especificar si la ordenación será ascendente o descendente.

Tenga en cuenta que si filtra usando el método ***where***, el método ***where*** debe invocarse antes del método ***orderBy***.

### <a name="paging"></a>Devolución de datos en páginas

El primer ejemplo muestra cómo seleccionar los cinco primeros elementos de una tabla. La consulta devuelve los elementos de una tabla de *ToDoItems*. *mToDoTable* es la referencia a la tabla de servicio móvil que ha creado previamente.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


A continuación, defina una consulta que omita los cinco primeros elementos y que, a continuación, devuelva los cinco siguientes.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Selección de columnas específicas

El código siguiente ilustra cómo devolver todos los elementos de una tabla de *ToDoItems*, pero solo muestra los campos *complete* y *text*. *mToDoTable* es la referencia a la tabla de servicio móvil que se ha creado previamente.

		mToDoTable.select("complete", "text").execute().get();


Aquí los parámetros para la función de selección son los nombres de las cadenas de las columnas de la tabla que desea devolver.

El método [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) tiene que seguir métodos como [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) y [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), si están presentes. Pueden ir seguidos de métodos como [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Concatenación de métodos de consulta

Los métodos usados en las tablas de servicios móviles de consulta se pueden concatenar. Esto le permite hacer operaciones como seleccionar columnas específicas de filas filtradas que se ordenan y paginan. Puede crear filtros lógicos bastante complejos.

Lo que hace este trabajo es que los métodos de consulta que usa devuelven objetos [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), que a su vez pueden tener métodos adicionales invocados en ellos. Para finalizar las series de métodos y ejecutar la consulta, llame al método [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

He aquí un código de ejemplo donde *mToDoTable* es una referencia a la tabla *ToDoItem* de Servicios móviles.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

El requisito principal para encadenar métodos es que el método *where* y los predicados vayan en primer lugar. Después de ello, puede llamar métodos subsiguientes en el orden que mejor satisfaga las necesidades de su aplicación.


##<a name="inserting"></a>Inserción de datos en un servicio móvil

El código siguiente muestra cómo insertar una nueva fila en una tabla.

En primer lugar, cree instancias de la clase *ToDoItem* y configure sus propiedades.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 A continuación, ejecute el siguiente código:

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Este código inserta el nuevo elemento y lo agrega al adaptador y se muestra en la interfaz de usuario.

Servicios móviles admite valores de cadena personalizados únicos para el id de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para la columna de Id. de una tabla de servicios móviles. Si desea identificar cada registro mediante una dirección de correo electrónico, podría usar el siguiente objeto JSON.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

Si no se proporciona un valor de identificador de cadena cuando se insertan nuevos registros en una tabla, Servicios móviles generará un valor exclusivo para el identificador.

La compatibilidad de los identificadores de cadena ofrece las siguientes ventajas a los desarrolladores:

+ Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta en la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

También puede usar scripts de servidor para configurar valores de identificador. El siguiente ejemplo de script genera un GUID personalizado y lo asigna al identificador de un registro nuevo. Esto es similar al valor de identificador que generaría Servicios móviles si no hubiera pasado un valor de identificador de registro.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Si una aplicación proporciona un valor para un identificador, Servicios móviles lo guardará como está. Esto incluye los espacios en blanco al principio o al final. Los espacios en blanco no se eliminarán del valor.

El valor `id` debe ser exclusivo y no debe incluir caracteres de los siguientes conjuntos:

+ Caracteres de control: [0x0000-0x001F] y [0x007F-0x009F]. Para obtener más información, consulte [Códigos de control ASCII C0 y C1].
+  Caracteres imprimibles: **"**(0 x 0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Los identificadores "." y ".."

También puede usar identificadores de números enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando `mobile table create` usando la opción `--integerId`. Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles].


##<a name="updating"></a>Actualización de los datos en un servicio móvil

El código siguiente muestra cómo actualizar los datos de una tabla. En este ejemplo, *item* es una referencia a una fila de la tabla *ToDoItem*, a la que se le han realizado algunos cambios. El método siguiente actualiza la tabla y el adaptador de interfaz de usuario.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Eliminación de datos en un servicio móvil

El código siguiente muestra cómo eliminar datos de una tabla. Elimina un elemento existente de la tabla ToDoItem que haya tenido la casilla de verificación **Completado** de la interfaz de usuario activada.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


El código siguiente ilustra otra forma de hacerlo. Elimina un elemento existente de la tabla ToDoItem especificando el valor del campo id de la fila que hay que eliminar (se supone que es igual a "2FA404AB-E458-44CD-BC1B-3BC847EF0902"). En una aplicación real, recogería el identificador de algún modo y lo pasaría como una variable. En este caso, para simplificar las pruebas, puede acceder al Portal de Azure clásico de su servicio, hacer clic en **Datos** y copiar un identificador con el que desee efectuar la prueba.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Búsqueda de un elemento específico
Puede que a veces quiera consultar un elemento específico por su *id*, a diferencia de las consultas donde normalmente se obtiene una serie de elementos que satisfacen ciertos criterios. El código siguiente muestra cómo hacer esto, para un *identificador* de valor `0380BAFB-BCFF-443C-B7D5-30199F730335`. En una aplicación real, recogería el identificador de algún modo y lo pasaría como una variable. En este caso, para simplificar las pruebas, puede acceder al Portal de Azure clásico de su servicio, hacer clic en la pestaña **Datos** y copiar un identificador con el que desee efectuar la prueba.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Trabajo con datos sin tipo

El modelo de programación sin tipo le da control exacto de la serialización de JSON. En determinadas situaciones podría necesitarlo, por ejemplo, si su tabla de servicios móviles contiene un gran número de columnas y solo necesita hacer referencia a algunas de ellas. Para usar el modelo escrito tiene que definir todas las columnas de la tabla del servicio móvil de su clase de datos. Sin embargo, con el modelo sin tipo solo tiene que definir las columnas que necesita usar.

La mayoría de las llamadas de API para obtener acceso a los datos son similares a las llamadas de programación con tipo. La diferencia principal es que en el modelo sin tipo se invocan métodos en el objeto **MobileServiceJsonTable**, en lugar del objeto **MobileServiceTable**.


### <a name="json_instance"></a>Creación de una instancia de tabla sin tipo

Como en el modelo con tipo, se empieza obteniendo una referencia de tabla, aunque en este caso se trate de un objeto [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). La referencia se obtiene llamando al método [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) en una instancia del cliente de Servicio móviles.

En primer lugar defina la variable:

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



Una vez que haya creado una instancia del cliente de Servicios móviles en el método **onCreate** (aquí, la variable *mClient*), deberá crear a continuación una instancia de **MobileServiceJsonTable**, con el código siguiente.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

Una vez que haya creado una instancia de **MobileServiceJsonTable**, puede llamar a casi todos los métodos disponibles con el modelo de programación con tipo. Sin embargo, en algunos casos los métodos toman un parámetro sin tipo, como podemos ver en los ejemplos siguientes.

### <a name="json_insert"></a>Inserción de una tabla sin tipo

El código siguiente muestra cómo realizar una inserción. El primer paso es crear un [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), que forma parte de la biblioteca de <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

El paso siguiente es insertar el objeto. La función de devolución de llamada pasada al método [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) es una instancia de la clase [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Observe que el parámetro del método *insert* es un JsonObject.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Si necesita obtener el identificador del objeto insertado, use esta llamada al método:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Eliminación de una tabla sin tipo

El código siguiente muestra cómo eliminar una instancia, en este caso, la misma instancia de un **JsonObject** que se creó en el ejemplo de *insert* anterior. Tenga en cuenta que el código es lo mismo que con el caso con tipo, pero el método tiene una firma diferente ya que hace referencia a un **JsonObject**.


         mToDoTable.delete(item);


También puede eliminar una instancia directamente usando su identificador:

		 mToDoTable.delete(ID);



### <a name="json_get"></a>Devolución de todas las filas de una tabla sin tipo

El código siguiente muestra cómo recuperar una tabla completa. Puesto que está utilizando una tabla de JSON, solamente puede recuperar de manera selectiva algunas de las columnas de la tabla.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Puede filtrar, ordenar y paginar concatenando métodos que tengan los mismos nombres que los usados en el modelo de programación con tipo.


##<a name="binding"></a>Enlace de datos a la interfaz de usuario

El enlace de datos implica tres componentes:

- el origen de datos
- el diseño de la pantalla
- y el adaptador que une a ambos

En nuestro código de ejemplo, devolvemos los datos de la tabla de servicio móvil *ToDoItem* en una matriz. Se trata de un patrón muy común para las aplicaciones de datos: las consultas de base de datos normalmente devuelven una colección de filas que el cliente recibe en una lista o matriz. En este ejemplo, la matriz es el origen de datos.

El código especifica un diseño de pantalla que define la vista de los datos que aparecerán en el dispositivo.

Y los dos están vinculados mediante un adaptador, que en este código es una extensión de la clase *ArrayAdapter&lt;ToDoItem&gt;*.

### <a name="layout"></a>Definición del diseño

El diseño lo definen varios fragmentos de código XML. Dado el diseño existente, supongamos que el código siguiente representa la **ListView** que queremos rellenar con nuestros datos de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


En el código anterior, el atributo *listitem* especifica el identificador del diseño para una fila concreta de la lista. Aquí está este código, que especifica una casilla y su texto asociado. Se crea una instancia de este por cada elemento de la lista. Un diseño más complejo especificaría campos adicionales en la pantalla. Este código está en el archivo *row\_list\_to\_do.xml*.

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>


### <a name="adapter"></a>Definición del adaptador

Como el origen de datos de nuestra vista es una matriz de *ToDoItem*, se realiza una subclase en nuestro adaptador desde una clase *ArrayAdapter&lt;ToDoItem&gt;*. Esta subclase producirá una vista para cada *ToDoItem* que use el diseño de *row\_list\_to\_do*.

En nuestro código definimos la clase siguiente, que es una extensión de la clase *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Debe reemplazar el método *getView* del adaptador. Este ejemplo de código es un ejemplo de cómo hacerlo: los detalles variarán con la aplicación.

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

		return row;
	}

Creamos una instancia de esta clase en nuestra actividad de la forma siguiente:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Fíjese en que el segundo parámetro para el constructor ToDoItemAdapter es una referencia al diseño. A la llamada al constructor le sigue este código, que obtiene en primer lugar una referencia a la **ListView** y luego llama a *setAdapter* para configurarse él mismo a fin de usar el adaptador que se acaba de crear:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Uso del adaptador

Ahora ya puede usar el enlace de datos. El código siguiente muestra cómo obtener los elementos de la tabla del servicio móvil, borrar el adaptador y, a continuación, llamar al método *add* del adaptador para llenarlo con los elementos devueltos.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

También debe llamar al adaptador en cualquier momento para modificar la tabla *ToDoItem* si desea que aparezcan los resultados. Como las modificaciones se realizan de registro en registro, se ocupará de una sola fila en lugar de una serie de ellas. Al insertar un elemento se llama al método *add* del adaptador; al eliminarlo, se llama al método *remove*.

##<a name="custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON. Para obtener un ejemplo de cómo crear una API personalizada en el servicio móvil, vea [Definición de un punto de conexión de API personalizada](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>Autenticación de usuarios

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, Microsoft Account, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en el back-end. Para obtener más información, vea [Introducción a la autenticación](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Se admiten dos flujos de autenticación: un *server* flow y un *client* flow. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

Para habilitar la autenticación en su aplicación tiene que realizar tres pasos:

- Registrar la aplicación para la autenticación con un proveedor y configurar Servicios móviles
- Restringir los permisos de tabla a los usuarios autenticados únicamente
- código de autenticación a su aplicación


Servicios móviles admite los siguiente proveedores de identidad existentes que podrá usar para autenticar a los usuarios:

- Cuenta Microsoft
- Facebook
- Twitter
- Google
- Azure Active Directory

Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar el identificador de un usuario autenticado para modificar las solicitudes.

Estas dos primeras tareas se realizan usando el [Portal de Azure clásico](https://manage.windowsazure.com/). Para obtener más información, vea [Introducción a la autenticación](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Adición del código de autenticación a su aplicación

1.  Agregue las siguientes instrucciones de importación al archivo de actividad de su aplicación.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. En el método **onCreate** de la clase de actividad, agregue la siguiente línea de código después del código que crea el objeto `MobileServiceClient`: se supone que la referencia al objeto `MobileServiceClient` es *mClient*.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    Este código autentica al usuario con el inicio de sesión de Google. Aparecerá un diálogo que muestra el identificador del usuario autenticado. No puede continuar sin una autenticación positiva.

    > [AZURE.NOTE]Si usa un proveedor de identidades que no sea Google, cambie el valor pasado al método **login** anterior a uno de los siguientes: _MicrosoftAccount_, _Facebook_, _Twitter_ o _WindowsAzureActiveDirectory_.


3. Cuando ejecute la aplicación, inicie sesión con el proveedor de identidades que haya elegido.


### <a name="caching"></a>Almacenamiento de tokens de autenticación en la memoria caché

Esta sección muestra cómo almacenar los tokens de autenticación en la caché. Hágalo para evitar que los usuarios tengan que volver a autenticarse si la aplicación está "hibernada" mientras el token sigue siendo válido.

El almacenamiento en caché de los tokens de autenticación requiere el almacenamiento del identificador de usuario y el token de autenticación localmente en el dispositivo. La próxima vez que se inicie la aplicación, compruebe la caché y, si estos valores están presentes, puede omitir el procedimiento de inicio de sesión y rehidratar el cliente con estos datos. No obstante, estos datos son confidenciales y deben almacenarse cifrados por seguridad en caso de que le roben el teléfono.

El siguiente fragmento de código demuestra la obtención de un token para el inicio de sesión de una cuenta Microsoft. El token se almacena en la memoria caché y se vuelve a cargar si se encuentra la memoria caché.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
	}


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined");
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined");
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


Entonces, ¿qué pasa si el token expira? En este caso, cuando intente usarlo para conectarse, recibirá la respuesta *401 unauthorized*. El usuario deberá pues iniciar sesión para obtener nuevos tokens. Es posible evitar la necesidad de escribir código para gestionar esto en cada sitio de su aplicación que llame a su servicio móvil utilizando filtros; así, podrá interceptar llamadas y respuestas de Servicios móviles. El código de filtro probará la respuesta para un 401, desencadenará el proceso de inicio de sesión si procede y, a continuación, reanudará la solicitud que generó el 401.


##<a name="customizing"></a>Personalización del cliente

Hay varias maneras de personalizar el comportamiento predeterminado del cliente de Servicios móviles.

### <a name="headers"></a>Personalización de encabezados de solicitud

Puede que desee adjuntar un encabezado personalizado a cada solicitud saliente. Para ello, configure **ServiceFilter** como se indica a continuación:

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Personalización de la serialización

Servicios móviles supone de forma predeterminada que los nombres de las tablas, las columnas y los tipos de datos del servidor coinciden exactamente con lo que aparece en el cliente. Sin embargo, puede haber un sinnúmero de motivos por los que los nombres del servidor y el cliente no coinciden. Por ejemplo, puede tener un cliente que ya existe y que desea cambiar para que use Servicios móviles en lugar de un producto de la competencia.

Es posible que desee realizar los siguientes tipos de personalizaciones:

- Los nombres de columna usados en la tabla del servicio móvil no coinciden con los nombres que usa en el cliente.
- Se usa una tabla de servicio móvil que tiene un nombre diferente a la clase que asigna en el cliente
- Se activa el uso automático de mayúsculas para las propiedades
- Se agregan propiedades complejas a un objeto

### <a name="columns"></a>Asignación de nombres de servidor y cliente diferentes

Supongamos que su código de cliente Java usa nombres de tipo Java estándar para las propiedades del objeto *ToDoItem*, como los que se indican a continuación.

- mId
- mText
- mComplete
- mDuration


Debe serializar los nombres de cliente en nombres JSON que coincidan con los nombres de columna de la tabla *ToDoItem* del servidor. El código siguiente, que usa la biblioteca <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>, lo hace.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Asignación de nombres de tabla diferentes entre el cliente y los servicios móviles

Asignar el nombre de la tabla de cliente a una tabla de servicios móviles diferente es fácil, simplemente usamos una de los reemplazos de la función <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, como se observa en el código siguiente.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Asignación automática de nombres de columna

Asignar nombres de columna para una tabla estrecha con solo unas pocas columnas no es muy difícil, como hemos visto en la sección anterior. Pero supongamos que nuestra tabla tiene muchas columnas, unas 20 o 30. Resulta que podemos llamar a la API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> y especificar una estrategia de conversión que se aplicará a todas las columnas, y así evitar tener que anotar cada nombre de columna única.

Para ello, usamos la biblioteca de <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> que utiliza la biblioteca de cliente Android en segundo plano para serializar los objetos de Java en datos JSON que, a su vez, se envían a Servicios móviles de Azure.

El código siguiente usa el método *setFieldNamingStrategy()*, en el que definimos un método *FieldNamingStrategy()*. Este método indica que hay que eliminar el carácter inicial (una "m") y, a continuación, poner el siguiente carácter en minúscula (en cada nombre de campo). Este código también habilita la impresión con sangría de JSON de salida.

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());



Este código debe ejecutarse antes que cualquier llamada de método en el objeto de cliente de Servicios móviles.

### <a name="complex"></a>Almacenamiento de una propiedad de objeto o matriz en una tabla

Hasta ahora todos nuestros ejemplos de serialización han implicado tipos primitivos, como enteros y cadenas, que se serializan fácilmente en JSON y en la tabla de Servicios móviles. Supongamos que queremos agregar un objeto complejo a nuestro tipo de cliente, que no se serializa automáticamente en JSON y la tabla. Pongamos que queremos agregar una matriz de cadenas al objeto cliente. Ahora debemos especificar cómo realizar la serialización y cómo almacenar la matriz en la tabla de servicios móviles.

Para ver un ejemplo de cómo hacerlo, consulte nuestra entrada de blog <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Personalización de la serialización con la biblioteca de <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> en el cliente Android de Servicios móviles</a>.

Este método general se puede usar siempre que tengamos un objeto complejo que no se serialice automáticamente en JSON y la tabla de servicios móviles.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Introducción a Servicios móviles]: mobile-services-android-get-started.md
[Códigos de control ASCII C0 y C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!---HONumber=AcomDC_0121_2016-->
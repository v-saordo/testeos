<properties
	pageTitle="Uso de la biblioteca de cliente de aplicaciones móviles de Android"
	description="Uso del SDK del cliente Android para aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/05/2016" 
	ms.author="ricksal"/>


# Uso de la biblioteca de cliente Android para Aplicaciones móviles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Esta guía muestra cómo utilizar el SDK de cliente Android para Aplicaciones móviles a fin de implementar escenarios comunes, como consultar datos (insertar, actualizar y eliminar), autenticar usuarios, controlar errores y personalizar el cliente. También realiza una profundización en el código de cliente común que se utiliza en la mayoría de aplicaciones móviles.

Esta guía se centra en el SDK de Android del cliente. Para más información sobre los SDK del lado servidor para aplicaciones móviles, vea [Trabajar con el SDK del back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) o [Cómo usar el SDK del back-end de Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).


<!---You can find the Javadocs API reference for the Android client library [here](http://go.microsoft.com/fwlink/p/?LinkId=298735).-->

## Configuración y requisitos previos

El SDK de Servicios móviles para Android admite la versión 2.2 de Android u otra posterior, aunque recomendamos que la compilación se lleve a cabo en la versión 4.2 o posterior.

Complete el tutorial [Inicio rápido de Aplicaciones móviles](app-service-mobile-android-get-started.md), que se asegurará de que ha instalado Android Studio; le ayudará a configurar su cuenta y crear su primer back-end de aplicación móvil. Si lo hace, puede omitir el resto de esta sección.

Si decide no completar el tutorial de Inicio rápido y desea conectar una aplicación Android a un back-end de aplicación móvil, debe hacer lo siguiente:

- [Crear un back-end de aplicación móvil](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) para utilizar con su aplicación Android (a menos que la aplicación ya tenga uno)
- En Android Studio, [actualizar los archivos de compilación de Gradle](#gradle-build), y
- [Habilitar permisos de Internet](#enable-internet)

Después de esto, debe completar los pasos descritos en la sección de profundización.

###<a name="gradle-build"></a>Actualización del archivo de compilación de Gradle 

Cambie ambos archivos **build.gradle**:

1. Agregue este código al archivo **build.gradle** del nivel *Project* dentro de la etiqueta *buildscript*:
 
		buildscript {
		    repositories {
		        jcenter()
		    }
		} 

2. Agregue este código al archivo **build.gradle** del nivel *Module app* dentro de la etiqueta *dependencies*:

		compile 'com.microsoft.azure:azure-mobile-android:3.0'

	Actualmente, la versión más reciente es 3.0. [Aquí](http://go.microsoft.com/fwlink/p/?LinkID=717034) se enumeran las versiones compatibles.

###<a name="enable-internet"></a>Habilitación de permisos de Internet
Para obtener acceso a Azure, la aplicación debe tener habilitado el permiso de INTERNET. Si aún no está habilitado, agregue la siguiente línea de código a su archivo **AndroidManifest.xml**:

	<uses-permission android:name="android.permission.INTERNET" />

## Profundización en los conceptos básicos  

Esta sección describe parte del código en la aplicación de inicio rápido. Si no completó el inicio rápido, deberá agregar este código a la aplicación.

> [AZURE.NOTE] La cadena "MobileServices" aparece con frecuencia en el código; sin embargo, el código hace realmente referencia al SDK de aplicaciones móviles (se trata simplemente de algo que se arrastra del pasado).


###<a name="data-object"></a>Definición de clases de datos de cliente

Para obtener acceso a datos de tablas de SQL Azure, se definen las clases de datos de cliente que corresponden a las tablas del back-end de la aplicación móvil. En los ejemplos de este tema se usa una tabla denominada *ToDoItem*, que tiene las siguientes columnas:

- id
- text
- complete

A continuación verá el objeto del cliente con tipo correspondiente:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

El código residirá en un archivo denominado **ToDoItem.java**.

Si la tabla de SQL Azure contuviera más columnas, agregaría los campos correspondientes a esta clase.

Por ejemplo, si tuviera una columna Prioridad de tipo entero, podría agregar este campo junto con sus métodos de captador y establecedor:

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }
	
	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

Para obtener información sobre cómo crear tablas adicionales en el back-end de aplicaciones móviles, vea [Cómo definir un controlador de tabla](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (back-end de. NET) o [Definición de tablas con un esquema dinámico](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (back-end de Node.js). Para un back-end de Node.js, también puede usar la configuración **Tablas fáciles** en el [Portal de Azure].

###<a name="create-client"></a>Creación del contexto de cliente

Este código crea el objeto **MobileServiceClient** que se usa para obtener acceso al back-end de la aplicación móvil. El código está en el método `onCreate` de la clase de **actividad** especificada en *AndroidManifest.xml* como una acción **PRINCIPAL** y una categoría **INICIADOR** En el código de inicio rápido, está en el archivo **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

En este código, reemplace `MobileAppUrl` por la dirección URL del back-end de la aplicación móvil, que se puede encontrar en el [Portal de Azure](https://portal.azure.com/) en la hoja del back-end de la aplicación móvil. Para que esta línea de código se compile, también debe agregar la siguiente instrucción **import**:

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Creación de una referencia de tabla

La forma más sencilla de consultar o modificar los datos del back-end es utilizar el *modelo de programación con tipo*, ya que Java es un lenguaje fuertemente tipado (más adelante trataremos el modelo *sin tipo*). Este modelo ofrece una perfecta serialización y deserialización de JSON usando la biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) al enviar datos entre objetos cliente y tablas en el Azure SQL del back-end: el desarrollador no tiene que hacer nada, el marco se encarga de todo.

Para obtener acceso a una tabla, cree primero un objeto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) mediante una llamada al método **getTable** en [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Este método tiene dos sobrecargas:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

En el código siguiente, *mClient* es una referencia al objeto MobileServiceClient.

La [primera sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296839) se utiliza cuando coinciden el nombre de clase y el nombre de tabla, y es la que se emplea en el inicio rápido:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


La [segunda sobrecarga](http://go.microsoft.com/fwlink/p/?LinkId=296840) se utiliza cuando el nombre de tabla es diferente del nombre de clase: el primer parámetro es el nombre de tabla.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Enlace de datos a la interfaz de usuario

El enlace de datos implica tres componentes:

- El origen de datos
- El diseño de la pantalla
- El adaptador que une a ambos

En nuestro código de ejemplo, devolvemos los datos de la tabla SQL Azure de Aplicaciones móviles *ToDoItem* en una matriz. Se trata de un patrón muy común para las aplicaciones de datos: las consultas de base de datos a menudo devuelven una colección de filas que el cliente recibe en una lista o matriz. En este ejemplo, la matriz es el origen de datos.

El código especifica un diseño de pantalla que define la vista de los datos que aparecerán en el dispositivo.

Y los dos están vinculados mediante un adaptador, que en este código es una extensión de la clase *ArrayAdapter&lt;ToDoItem&gt;*.

#### <a name="layout"></a>Definición del diseño

El diseño lo definen varios fragmentos de código XML. Dado el diseño existente, supongamos que el código siguiente representa la **ListView** que queremos rellenar con nuestros datos de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


En el código anterior, el atributo *listitem* especifica el identificador del diseño para una fila concreta de la lista. Aquí está este código, que especifica una casilla y su texto asociado. Se crea una instancia de este por cada elemento de la lista. Este diseño no muestra el campo **id**, y un diseño más complejo especificaría campos adicionales en la pantalla. Este código está en el archivo **row\_list\_to\_do.xml**.

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


#### <a name="adapter"></a>Definición del adaptador

Como el origen de datos de nuestra vista es una matriz de *ToDoItem*, se realiza una subclase en nuestro adaptador desde una clase *ArrayAdapter&lt;ToDoItem&gt;*. Esta subclase producirá una vista para cada *ToDoItem* que use el diseño de *row\_list\_to\_do*.

En nuestro código definimos la clase siguiente, que es una extensión de la clase *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Debe reemplazar el método *getView* del adaptador. Este ejemplo de código es un ejemplo de cómo hacerlo: los detalles variarán con la aplicación.

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Creamos una instancia de esta clase en nuestra actividad de la forma siguiente:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Fíjese en que el segundo parámetro para el constructor ToDoItemAdapter es una referencia al diseño. A la llamada al constructor le sigue este código, que obtiene en primer lugar una referencia a la **ListView** y luego llama a *setAdapter* para configurarse él mismo a fin de usar el adaptador que se acaba de crear:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>La estructura de API

Las operaciones de tabla de Aplicaciones móviles y las llamadas de API personalizadas son asincrónicas; así pues, se usan los objetos [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) y [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) en todos los métodos asincrónicos que implican consultas e inserciones, actualizaciones y eliminaciones. Esto facilita la realización de varias operaciones en un subproceso en segundo plano sin tener que tratar con varias devoluciones de llamada anidadas.

Para ver cómo se utilizan estas API asincrónicas en su aplicación Android y cómo se muestran los datos en la interfaz de usuario, revise el archivo **ToDoActivity.java** en el proyecto de inicio rápido de Android desde el [Portal de Azure].


#### <a name="use-adapter"></a>Uso del adaptador

Ahora ya puede usar el enlace de datos. El código siguiente muestra cómo obtener los elementos de la tabla del servicio móvil, borrar el adaptador y, a continuación, llamar al método *add* del adaptador para completarlo con los elementos devueltos.


**Por determinar**: probar este código.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

También debe llamar al adaptador en cualquier momento para modificar la tabla *ToDoItem* si desea que aparezcan los resultados. Como las modificaciones se realizan de registro en registro, se ocupará de una sola fila en lugar de una serie de ellas. Al insertar un elemento se llama al método *add* del adaptador; al eliminarlo, se llama al método *remove*.

##<a name="querying"></a>Consulta de datos desde el back-end de la aplicación móvil

En esta sección se describe cómo generar consultas al back-end de la aplicación móvil, lo cual incluye las siguientes tareas:

- [Devolver todos los elementos]
- [Filtro de datos devueltos]
- [Ordenar datos devueltos]
- [Devolver datos en páginas]
- [Seleccionar columnas específicas]
- [métodos de consulta](#chaining)

### <a name="showAll"></a>Devolver todos los elementos de una tabla

La consulta siguiente devuelve todos los elementos de la tabla *ToDoItem*.

	List<ToDoItem> results = mToDoTable.execute().get();             

La variable *results* devuelve el conjunto de resultados de la consulta como una lista.

### <a name="filtering"></a>Filtro de datos devueltos

La siguiente ejecución de consulta siguiente devuelve todos los elementos de la tabla *ToDoItem* donde *complete* es igual a *false*. Este es el código que ya está en el inicio rápido.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* es la referencia a la tabla de servicio móvil que se ha creado previamente.

Se define un filtro mediante la llamada al método **where** en la referencia de tabla. A esto le sigue una llamada de método **field** seguida de una llamada de método que especifica el predicado lógico. Los métodos de predicado posibles incluyen **eq** (igual a), **ne** (no igual a), **gt** (mayor que), **ge** (mayor o igual que), **lt** (menor que), **le** (menor o igual que), etc. Estos métodos permiten comparar campos de número y cadena con valores específicos.

Puede filtrar por fechas. Los siguientes métodos permiten comparar el campo de fecha entero o partes de la fecha: **year**, **month**, **day**, **hour**, **minute** y **second**. El ejemplo siguiente agrega un filtro para los elementos cuyo valor *due date* sea igual a 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

Los métodos siguientes admiten filtros complejos en campos de cadena: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** y **length**. El ejemplo siguiente filtra las filas de tabla donde la columna *text* empieza por "PRI0".

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Se admiten los siguientes métodos de operador en los campos de número: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling**, and **round**. El ejemplo siguiente filtra las filas de tabla cuya columna *duration* empieza por un número par.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Puede combinar predicados con estos métodos lógicos: **and**, **or** y **not**. En el ejemplo siguiente se combinan dos de los ejemplos anteriores.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

Y puede agrupar y anidar operadores lógicos, como este:

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Para obtener más información y ver ejemplos de filtrado, consulte [Exploring the richness of the Android client query model (Exploración de la riqueza del modelo de consulta del cliente Android)](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Clasificación de datos devueltos

El código siguiente devuelve todos los elementos de una tabla de *ToDoItems* cuyo campo *text* sigue un orden ascendente. *mToDoTable* es la referencia a la tabla del back-end que ha creado anteriormente:

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

El primer parámetro del método **orderBy** es una cadena igual al nombre del campo por el que realizar la ordenación.

El segundo parámetro usa la enumeración **QueryOrder** para especificar si la ordenación será ascendente o descendente.

Tenga en cuenta que si filtra usando el método ***where***, el método ***where*** debe invocarse antes del método ***orderBy***.

### <a name="paging"></a>Devolución de datos en páginas

El primer ejemplo muestra cómo seleccionar los cinco primeros elementos de una tabla. La consulta devuelve los elementos de una tabla de *ToDoItems*. *mToDoTable* es la referencia a la tabla de back-end que ha creado previamente.

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Aquí se muestra una consulta que omite los cinco primeros elementos y que, a continuación, devuelve los cinco siguientes:

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Selección de columnas específicas

El código siguiente ilustra cómo devolver todos los elementos de una tabla de *ToDoItems*, pero solo muestra los campos *complete* y *text*. *mToDoTable* es la referencia a la tabla de back-end que se ha creado previamente.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


Aquí los parámetros para la función de selección son los nombres de las cadenas de las columnas de la tabla que desea devolver.

El método **select** tiene que seguir métodos como **where** y **orderBy**, si están presentes. Pueden ir seguidos de métodos de paginación como **top**.

### <a name="chaining"></a>Concatenación de métodos de consulta

Como hemos visto, se pueden concatenar los métodos usados en la consulta de tablas de back-end. Esto le permite hacer operaciones como seleccionar columnas específicas de filas filtradas que se ordenan y paginan. Puede crear filtros lógicos bastante complejos.

Lo que hace este trabajo es que los métodos de consulta que se usan devuelvan objetos **MobileServiceQuery&lt;T&gt;**, que a su vez pueden tener métodos adicionales invocados en ellos. Para finalizar las series de métodos y ejecutar la consulta, llame al método **execute**.

He aquí un código de ejemplo donde *mToDoTable* es una referencia a la tabla *ToDoItem*.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

El requisito principal para encadenar métodos es que el método *where* y los predicados vayan en primer lugar. Después de ello, puede llamar métodos subsiguientes en el orden que mejor satisfaga las necesidades de su aplicación.


##<a name="inserting"></a>Inserción de datos en el back-end

El código siguiente muestra cómo insertar una nueva fila en una tabla.

En primer lugar, cree instancias de la clase *ToDoItem* y configure sus propiedades.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

A continuación, ejecute el siguiente código:

	ToDoItem entity = mToDoTable.insert(item).get();

La entidad devuelta coincide con los datos insertados en la tabla de back-end, incluido el identificador y los otros valores establecidos en el back-end.

Aplicaciones móviles requiere que cada tabla tenga una columna denominada **id**, que se utiliza para indexar la tabla. De forma predeterminada, esta columna es un tipo de datos de cadena, que se requiere para admitir la sincronización sin conexión. El valor predeterminado de la columna de identificador es un GUID, pero puede proporcionar otros valores únicos, como nombres de usuario o direcciones de correo electrónico. Cuando no se proporciona un valor de identificador de cadena para un registro insertado, el back-end genera un nuevo valor GUID.

Los valores de identificador de cadena proporcionan las siguientes ventajas:

+ Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta a la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores se integran mejor con una lógica de aplicación.

##<a name="updating"></a>Actualización de los datos en una aplicación móvil

El código siguiente muestra cómo actualizar los datos de una tabla.

    mToDoTable.update(item).get();

En este ejemplo, *item* es una referencia a una fila de la tabla *ToDoItem*, a la que se le han realizado algunos cambios.

##<a name="deleting"></a>Eliminación de datos en una aplicación móvil

El código siguiente muestra cómo eliminar datos de una tabla especificando el objeto de datos.

	mToDoTable.delete(item);

También puede eliminar un elemento especificando el campo **id** de la fila que se va a eliminar.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);
                    

##<a name="lookup"></a>Búsqueda de un elemento específico

Este código muestra cómo buscar un elemento con un *id* determinado.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Trabajo con datos sin tipo

El modelo de programación sin tipo le da control exacto de la serialización de JSON. En determinadas situaciones podría necesitarlo, por ejemplo, si su tabla de back-end contiene un gran número de columnas y solo necesita hacer referencia a algunas de ellas. Para usar el modelo tipado tiene que definir todas las columnas de la tabla de las aplicaciones móviles en su clase de datos. Sin embargo, con el modelo sin tipo solo tiene que definir las columnas que necesita usar.

La mayoría de las llamadas de API para obtener acceso a los datos son similares a las llamadas de programación con tipo. La diferencia principal es que en el modelo sin tipo se invocan métodos en el objeto **MobileServiceJsonTable**, en lugar del objeto **MobileServiceTable**.


### <a name="json_instance"></a>Creación de una instancia de tabla sin tipo

Como en el modelo con tipo, se empieza obteniendo una referencia de tabla, aunque en este caso se trate de un objeto **MobileServicesJsonTable**. La referencia se obtiene llamando al método **getTable** en una instancia del cliente, como esta:

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Una vez que haya creado una instancia de **MobileServiceJsonTable**, puede llamar a casi todos los métodos disponibles con el modelo de programación con tipo. Sin embargo, en algunos casos los métodos toman un parámetro sin tipo, como podemos ver en los ejemplos siguientes.

### <a name="json_insert"></a>Inserción de una tabla sin tipo

El código siguiente muestra cómo realizar una inserción. El primer paso es crear un [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), que forma parte de la biblioteca de [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

El paso siguiente es insertar el objeto.

    mJsonToDoTable.insert(jsonItem).get();                   


Si necesita obtener el identificador del objeto insertado, use esta llamada al método:

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Eliminación de una tabla sin tipo

El código siguiente muestra cómo eliminar una instancia, en este caso, la misma instancia de un **JsonObject** que se creó en el ejemplo de *insert* anterior. Tenga en cuenta que el código es lo mismo que con el caso con tipo, pero el método tiene una firma diferente ya que hace referencia a un **JsonObject**.


         mToDoTable.delete(jsonItem);


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



##<a name="custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Desde un cliente Android, llame al método **invokeApi** para llamar al punto de conexión de API personalizado. En el ejemplo siguiente se muestra cómo llamar a un punto de conexión de API denominado *completeAll*, que devuelve una clase de colección denominada MarkAllResult.

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class ); 
	    	
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
	
El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores. Otras versiones de **invokeApi** le permiten enviar opcionalmente un objeto en el cuerpo de solicitud, especificar el método HTTP y enviar parámetros de consulta con la solicitud. También se proporcionan versiones sin tipo de **invokeApi**.

##<a name="authentication"></a>Incorporación de autenticación a la aplicación

Los tutoriales ya describen detalladamente cómo agregar estas características.

Servicio de aplicaciones es compatible con la [autenticación de los usuarios de aplicaciones](mobile-services-android-get-started-users.md) mediante diversos proveedores de identidades externas: Facebook, Google, cuenta Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en el back-end.

Se admiten dos flujos de autenticación: un *server* flow y un *client* flow. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de clientes permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor, y requiere que lo codifique.

Para habilitar la autenticación en su aplicación tiene que realizar tres pasos:

- Registrar la aplicación para la autenticación con un proveedor, y configurar su back-end de aplicación móvil.
- Restringir los permisos de tabla a solo los usuarios autenticados.
- Agregar código de autenticación a su aplicación.

Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar el SID de un usuario autenticado para modificar las solicitudes.

Estas dos primeras tareas se realizan usando el [Portal de Azure](https://portal.azure.com/). Para obtener más información, vea [Introducción a la autenticación].

### <a name="caching"></a>Adición del código de autenticación a su aplicación

El código siguiente inicia el proceso de inicio de sesión del flujo de servidor mediante el proveedor de Google:

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Puede obtener el identificador del usuario que ha iniciado sesión desde un **MobileServiceUser** utilizando el método **getUserId**. Para obtener un ejemplo de cómo usar Futures para llamar a las API de inicio de sesión asincrónico, vea [Introducción a la autenticación].


### <a name="caching"></a>Almacenamiento de tokens de autenticación en la memoria caché

El almacenamiento en caché de los tokens de autenticación requiere el almacenamiento del identificador de usuario y el token de autenticación localmente en el dispositivo. La próxima vez que se inicie la aplicación, compruebe la caché y, si estos valores están presentes, puede omitir el procedimiento de inicio de sesión y rehidratar el cliente con estos datos. No obstante, estos datos son confidenciales y deben almacenarse cifrados por seguridad en caso de que le roben el teléfono.

Puede ver un ejemplo completo de cómo almacenar en memoria caché los tokens de autenticación en la [sección sobre el almacenamiento en caché de tokens de autenticación](app-service-mobile-android-get-started-users.md#cache-tokens).

Si intenta utilizar un token caducado, recibirá como respuesta *401 unauthorized*. El usuario deberá pues iniciar sesión para obtener nuevos tokens. Es posible evitar la necesidad de escribir código para gestionar esto en cada sitio de su aplicación que llame a su servicio móvil utilizando filtros; así, podrá interceptar llamadas y respuestas de Servicios móviles. El código de filtro probará la respuesta para un 401, desencadenará el proceso de inicio de sesión si procede y, a continuación, reanudará la solicitud que generó el 401. También puede inspeccionar el token para comprobar la caducidad.


## <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. Esta opción es con frecuencia preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia UX más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.

2. Instale AAL modificando el archivo build.gradle para incluir lo siguiente:

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. Agregue el siguiente código a la aplicación y realice las siguientes sustituciones:

* Sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Sustituya **INSERT-RESOURCE-ID-HERE** por el id. de cliente del back-end de la aplicación móvil. Puede obtenerlo en la pestaña **Avanzadas** en **Configuración de Azure Active Directory** en el portal.

* Sustituya **INSERT-CLIENT-ID-HERE** por el id. de cliente que copió de la aplicación cliente nativa.

* Sustituya **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debería ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## Procedimiento: Incorporación de una notificación push a la aplicación

Puede [leer información general](notification-hubs-overview.md/#integration-with-app-service-mobile-apps) en la que se describe cómo los Centros de notificaciones de Microsoft Azure admiten una amplia variedad de notificaciones push.

En [este tutorial](app-service-mobile-android-get-started-push.md), cada vez que se inserta un registro, se envía una notificación push.

## Incorporación de sincronización sin conexión a la aplicación
El tutorial de inicio rápido contiene código que implementa la sincronización sin conexión. Busque código que tenga comentarios como este en el prefijo:

	// Offline Sync

Al quitar el comentario de las siguientes líneas de código se puede implementar la sincronización sin conexión, y se puede agregar código similar a otro código de aplicaciones móviles.

##<a name="customizing"></a>Personalización del cliente

Hay varias maneras de personalizar el comportamiento predeterminado del cliente.

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

El cliente supone que los nombres de tabla, los nombres de columna y los tipos de datos del back-end coinciden todos exactamente con los objetos de datos definidos en el cliente. Sin embargo, puede haber un sinnúmero de motivos por los que los nombres del servidor y el cliente no coinciden. En su situación, es posible que desee realizar los siguientes tipos de personalizaciones:

- Los nombres de columna usados en la tabla del servicio móvil no coinciden con los nombres que usa en el cliente.
- Usar una tabla de servicio móvil que tenga un nombre diferente que la clase a la que se asigna en el cliente.
- Activar el uso automático de mayúsculas para las propiedades.
- Agregar propiedades complejas a un objeto.

### <a name="columns"></a>Asignación de nombres de servidor y cliente diferentes

Supongamos que su código de cliente Java usa nombres de tipo Java estándar para las propiedades del objeto *ToDoItem*, como los que se indican a continuación.

- mId
- mText
- mComplete
- mDuration


Debe serializar los nombres de cliente en nombres JSON que coincidan con los nombres de columna de la tabla *ToDoItem* del servidor. El código siguiente, que usa la biblioteca [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801), lo hace.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Asignación de nombres de tabla diferentes entre el cliente y el back-end

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
[Devolver todos los elementos]: #showAll
[Filtro de datos devueltos]: #filtering
[Ordenar datos devueltos]: #sorting
[Devolver datos en páginas]: #paging
[Seleccionar columnas específicas]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal de Azure]: https://portal.azure.com
[Introducción a la autenticación]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0128_2016-->
<properties 
	pageTitle="Caso práctico de programador de Búsqueda de Azure: Cómo creó WhatToPedia un portal infomedia en Microsoft Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Obtenga información acerca de cómo crear un portal de información y un motor de búsqueda meta con Búsqueda de Azure, un servicio de búsqueda hospedado en la nube para desarrolladores." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="02/18/2016" 
	ms.author="heidist"/>

# Caso práctico para desarrolladores de Búsqueda de Azure

## ¿Cómo crea [WhatToPedia.com](http://whattopedia.com/) un portal infomedia en Microsoft Azure

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">La gran idea</font>


Nuestra idea es crear un portal de información que ayude a los compradores a conectar con los distribuidores, a través de una experiencia muy relevante de búsqueda de ámbito, similar a como los portales de viajes establecen correspondencias entre turistas y hoteles, restaurantes y opciones de entretenimiento en territorios no registrados.

El portal que imaginamos proporcionará una experiencia de búsqueda de una calidad excepcionalmente alta sobre los datos de un distribuidor en un determinado mercado, que ayuda a los compradores a encontrar tiendas según la ubicación y otros servicios proporcionados por el distribuidor. Se va a inicializar el motor de búsqueda con un conjunto de datos inicial, pero se creará un mayor valor con el paso del tiempo, con la ayuda de los suscriptores distribuidores que publican información acerca de sus empresas. Promociones, nuevos productos, marcas populares, servicios internos de especialidades (todos ejemplos de datos que agregan valor a nuestro sitio). Estos datos se notifican automáticamente e integran en el corpus de búsqueda una vez que el distribuidor se suscribe a un suscriptor. La publicidad y el modelo de suscripción proporcionan el flujo de ingresos para nuestra nueva empresa.

Búsqueda será el modelo de interacción de usuarios predominante en una plataforma de nube pura. Para fines de ampliación y obtención de costes reducidos, casi todo lo que hacemos, desde la experiencia del portal hasta el control de orígenes, se realizará a través de un servicio en línea. Mediante el uso de un motor de búsqueda que proporciona las funciones que necesitamos de fábrica, podemos crear una aplicación de búsqueda rápidamente, sin tener que crear y administrar un motor de búsqueda nosotros mismos.

## ¿Qué creamos?

WhatToPedia es una empresa de infomedia de inicio. Creamos [WhatToPedia.com](http://whattopedia.com/) (actualmente en el mercado de prueba en el norte de Europa y con una fecha de puesta en marcha para el 2 de febrero de 2015. Nuestra base de clientes está compuesta principalmente por tiendas de ladrillos y mortero que necesitan disponer de una presencia en línea asequible y fácil de administrar y mantener.

Nuestra tarea es atraer a los compradores a través de una experiencia de búsqueda en línea excelente, potenciando los resultados en función de la ciudad o el vecindario, las marcas, los nombres o los tipos de tiendas. Atraer a compradores tiene un efecto dominó que motiva a los distribuidores a suscribirse a nuestro sitio del portal. Las suscripciones son de pago y presentan un precio asequible.

 ![][7]

Después de suscribirse, un distribuidor toma control de su perfil existente (creado inicialmente por nosotros a partir de los datos adquiridos), actualizándolo con datos adicionales sobre promociones, marcas destacadas o anuncios. Las funcionalidades internas, como los idiomas hablados, las monedas aceptadas, las compras libres de impuestos, pueden notificarse automáticamente para atraer mejor a los compradores que buscan dichos servicios.

## Quiénes somos

Me llamo Thomas Segato (Microsoft Consulting) y trabajé con Jesper Boelling, desarrollador jefe en WhatToPedia, para diseñar la solución.

WhatToPedia es una start-up que está probando el marketing de su nuevo portal en Suecia, donde la mayoría de los 60.000 distribuidores son PYMES de ladrillos y mortero. Debido a que sabemos que las personas que compran en Europa hablan varios idiomas y disponen de varias monedas, construimos soluciones que se adaptan a los compradores de varios idiomas. Necesitábamos y encontramos un motor de búsqueda compatible con nuestros requisitos multilingües de Búsqueda de Azure.

Búsqueda de Azure resultó un punto de inflexión para nuestro proyecto. Antes de que se encontrase disponible la Búsqueda de Azure, dedicamos mucha energía a trabajar a construir nuestro propio motor de búsqueda. Disponer de Búsqueda de Azure como servicio en línea permitió eliminar los principales obstáculos técnico y administrativos de nuestra solución, lo que nos permitía llegar al mercado con mayor rapidez y con una experiencia de búsqueda más sólida.

## ¿Cómo lo hicimos?

Nuestra visión era crear una infraestructura completa basada únicamente en servicios en la nube. Se seleccionó a Microsoft como la plataforma estratégica porque era el proveedor que ofrecía los servicios necesarios (para la colaboración y el desarrollo), escala a petición y precios asequibles.
 
### Componentes de alto nivel

Creamos una empresa, no solo un sitio. Para todo ello se necesitaba una gama completa de herramientas y aplicaciones. Adoptamos Visual Studio y Visual Studio Team Services para efectuar el desarrollo, Team Foundation Service (TFS) Online para el control de orígenes y la administración de scrum, Office 365 para la comunicación y la colaboración y, por supuesto, Microsoft Azure para todas las operaciones relacionadas con el sitio y el almacenamiento. Con Visual Studio, el IDE proporcionaba aprovisionamiento directo a Azure, y la integración en TFS Online proporcionaba un aumento adicional de la productividad.

El siguiente diagrama ilustra los componentes de alto nivel usados en la infraestructura de WhatToPedia.

   ![][8]

### Cómo se usa Microsoft Azure

Si examina los cuadros verdes en el diagrama anterior, verá que la solución de WhatToPedia se crea en estos servicios:

- [Búsqueda de Azure](https://azure.microsoft.com/services/search/)
- [Sitios web de Azure que usan MVC 4](https://azure.microsoft.com/services/websites/)
- [WebJobs de Azure para las tareas programadas](../app-service-web/websites-webjobs-resources.md)
- [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/)
- [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/)
- [Servicio de correo electrónico SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

La esencia de la solución son los datos y la búsqueda. A continuación se muestra el flujo de datos del proveedor del distribuidor al cliente final:

  ![][9]

El almacenamiento de datos principal son los datos del distribuidor y de cuentas de la base de datos de Base de datos SQL de Azure. Esto está compuesto por el conjunto de datos inicial, junto con los datos específicos del distribuidor agregados con el tiempo. Vamos a usar un WebJob de Azure para publicar actualizaciones de la Base de datos SQL en el corpus de búsqueda de Búsqueda de Azure.

### Capa de presentación

El portal es un sitio web de Azure, implementado en MVC 4 y en [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Elegimos MVC porque ofrece un enfoque mucho más limpio de HTML que el desarrollo basado en formas de ASP.NET. Para evitar tener que crear aplicaciones para varios dispositivos y mantener varias plataformas móviles, Twitter Bootstrap ha optado por admitir todos los dispositivos y plataformas.

### Autenticación, permisos y datos confidenciales

Los compradores exploran el sitio de forma anónima. Como tal, no hay ningún requisito de inicio de sesión para los compradores ni almacenamos los datos del consumidor.

Los distribuidores son otra historia. En este caso, almacenamos la información de perfil público, la información de facturación y el contenido multimedia que desean exponer en el sitio. Cada distribuidor que se suscribe al sitio obtiene un inicio de sesión de usuario, usado para autenticar al usuario antes de realizar actualizaciones en el perfil del suscriptor. Almacenamos todos los datos del suscriptor de forma segura en el almacenamiento de Base de datos de SQL de Azure y BLOB de Azure. Hemos optado por un modelo de autenticación basado en la autenticación basada en formularios .NET. Elegimos este enfoque por su simplicidad. No necesitábamos los roles, la compatibilidad con la interfaz de usuario ni otras funciones externas que se proporcionan con otros enfoques.

Para asegurarse de que los distribuidores solo vean los datos que les pertenecen, creamos un ID de distribuidor para cada distribuidor que se usará posteriormente en todas las operaciones de lectura y escritura relacionadas con datos específicos del distribuidor. Con este enfoque, descubrimos que no necesitábamos conceder permisos para las bases de datos a distribuidores individuales. Todos los distribuidores interactúan con el sistema con un rol de base de datos único, con el Id. de distribuidor como técnica de aislamiento de datos.

Dado que nuestro negocio depende de los efectos de bajada (que permite aumentar el nivel de negocio de los distribuidores, creando incentivos para anunciarse y suscribirse), podemos trazar la línea en la gestión de compras a través de la web. De por sí, no encontrará un carro de la compra en nuestro sitio, lo que simplifica los requisitos de seguridad.

Otra simplificación que empleamos fue externalizar nuestras operaciones de facturación y cuentas de pago. Mediante el envío de información de pago de clientes directamente a un tercero ([SveaWebPay](http://www.sveawebpay.se/)), se reducen los riesgos asociados con el almacenamiento y la protección de datos confidenciales de nuestros almacenes de datos.

### Motor de búsqueda

El núcleo de nuestra solución es el motor de búsqueda que se basa en el servicio Búsqueda de Azure. Inicialmente, creamos un motor de búsqueda personalizado, pero durante este proceso, nos dimos cuenta de que la complejidad y el esfuerzo eran muy elevados, lo cual nos llevó a considerar otras alternativas.

Entre las funciones básicas que resultaban más importantes para nosotros se incluían:

- Filtros
- Navegación por facetas
- Potenciación de resultados
- Paginación a través de AJAX

Una búsqueda realizada en Internet nos llevó hasta el vídeo siguiente, que nos inspiró para probar Búsqueda de Azure: [Deep Dive at TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)

Después de ver el vídeo, estábamos listos para crear un prototipo basado en lo que vimos. Dado que ya disponíamos de un modelo de datos en MVC, crear el prototipo resultó sencillo ya que los datos contenían términos que se podían buscar y ya habíamos calculado los requisitos sobre cómo deseábamos ordenar, facetar y filtrar los datos.

Así es como creamos el prototipo.

**Configuración del servicio de Búsqueda de Azure**

1. Inicie sesión en el Portal de Azure clásico y agregue el servicio de búsqueda a la suscripción. Se utilizó la versión compartida (gratis con la suscripción).
2. Creación de un índice. Para el prototipo, utilizamos la IU del portal para definir los campos de búsqueda y crear los perfiles de puntuación. Nuestro perfil de puntuación está basado en los datos de ubicación: país | ciudad |dirección (consulte: Agregar perfiles de puntuación).
3. Copie la dirección URL del servicio y la clave de la API de administración en nuestros archivos de configuración. Esta clave se encuentra en la página del servicio de búsqueda del portal y se usa para autenticarse en el servicio.
	
**Desarrollar un trabajo de indexador de búsqueda: consola de Windows**

1. Lea todos los distribuidores de la base de datos.
2. Llame a la API de servicio de Búsqueda de Azure para cargar los distribuidores uno a uno (consulte: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Establezca una propiedad en la base de datos mediante la que el distribuidor aparezca como indexado para la indexación incremental. Hemos llevado a cabo esto agregando un campo «indexador» que almacena el estado del índice de cada perfil (indexado o no). 

Consulte el apéndice del fragmento de código que crea el trabajo de indexador.

**Desarrollar un portal web de búsqueda: MVC**

1. Llame al servicio de Búsqueda de Azure para obtener todos los documentos de búsqueda (consulte: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extraiga lo siguiente de la respuesta del servicio de búsqueda (mediante el uso de json.net http://james.newtonking.com/json)
   - Resultados
   - Facetas
   - Recuentos de resultado
   - Desarrolle una interfaz de usuario para mostrar los resultados de la búsqueda, las facetas y los recuentos (ya disponíamos de esto).

Este es el código que usamos para obtener los resultados de Búsqueda de Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Potenciación por ubicación**

Probablemente el requisito más importante que es necesario comprobar en el prototipo incluía agregar una palabra clave de búsqueda de ubicación a la consulta. Es fundamental para nuestro portal que si un usuario escribe un nombre de ciudad en la consulta de búsqueda, que los distribuidores de dicha ciudad se clasificasen por encima de los distribuidores que tengan la palabra clave de la ciudad en la descripción. Para este requisito, se usó un perfil de puntuación para clasificar el campo de ciudad por encima de otros campos.

**Compatibilidad con varios idiomas**

Necesitábamos mostrar resultados correctos en los idiomas correctos y proporcionar una opción para obtener los mismos resultados en diferentes idiomas. Las dos partes de este problema fueron:

- La búsqueda de palabras en varios idiomas
- La visualización de resultados de búsqueda en el idioma correcto

Resolvimos la parte de la presentación mediante la adición de un documento para cada idioma con texto localizado y una propiedad con el idioma. Cuando un usuario escribe un término de búsqueda, usamos `$filter` expresiones para filtrar según el idioma elegido por el usuario.

Cada uno de los documentos tiene una propiedad oculta denominada "cities" en el tipo de colección. Esta propiedad almacena nombres de ciudades en todos los idiomas, lo que permite al usuario buscar en varios idiomas.

###Almacenamiento de datos

Todos los datos (perfil, suscripción y contabilidad) se almacenan en la Base de datos SQL. Todos los archivos multimedia se almacenan en el almacenamiento de BLOB de Azure, incluidas imágenes y vídeos proporcionados por el distribuidor. Mediante el uso del almacenamiento BLOB independiente, se aíslan los efectos de carga de archivos; los archivos nunca están mezclados con el sitio web, por lo que no es necesario volver a generar el sitio cuando agregamos archivos.

Una ventaja importante de nuestro diseño del almacenamiento es que varios desarrolladores pueden compartir un almacenamiento de desarrollo único. Uno de los requisitos del proyecto WhatToPedia era poder crear un entorno de desarrollo antes de 15 minutos, incluidos vídeos, imágenes y datos del distribuidor. Mediante la obtención de los datos más recientes de TFS Online, la ejecución de un script SQL y la ejecución del trabajo de importación, puede establecerse un entorno completo en muy poco tiempo. Esta práctica también mejora el proceso de ensayo.

###WebJobs

Usamos WebJobs de Azure para actualizar datos en el índice. Al crear un trabajo de indexador de búsqueda, la parte de indexación era muy fácil de integrar en nuestra solución. El único cambio de código que hicimos fue acomodar el trabajo de indexador para agregar un campo `Indexed` a nuestro modelo de datos para indicar el estado del índice. Cuando se agrega o actualiza un nuevo perfil, el campo `Indexed` se establece en false. Lo mismo se aplica si el distribuidor cambia sus datos de perfil a través del portal.

El trabajo busca todas las filas que tengan `Indexed` establecido en false. Cuando encuentra la fila, el documento se publica en la Búsqueda de Azure y, a continuación, el campo `Indexed` se establece en true. No tuvimos que planear la adición frente a la actualización de datos porque el servicio de Búsqueda de Azure ya se encarga de esto. Si agrega un documento que ya está presente, el servicio realizará una actualización automáticamente.

Todos los trabajos web se han desarrollado como aplicaciones de consola que se pueden cargar en sitios web de Azure como archivos ZIP, descomprimidos y, a continuación, programados.

El trabajo está programado para ejecutarse cada 5 minutos como una tarea web programada. Hemos calculado que el servicio tarda aproximadamente tres minutos en cargar 3.000 documentos, lo cual estaba dentro de nuestros requisitos.

> [AZURE.NOTE] Hay una función de indexador prototipo introducida recientemente en la Búsqueda de Azure. Esta función llegó demasiado tarde para introducirla en la primera versión, pero parece resolver el mismo problema para el que usamos nuestro trabajo de indexador, que es automatizar las operaciones de carga de datos.


###Estrategia de copia de seguridad

Hemos diseñado una estrategia de copia de seguridad en múltiples niveles para recuperarse de una amplia variedad de escenarios, desde errores graves hasta la recuperación de una transacción individual. Entre los recursos a proteger se incluyen tres tipos de datos (sitio web, datos del suscriptor y archivos multimedia).

En primer lugar, al mantener el código de origen del sitio web en TFS Online, sabemos que si el sitio deja de funcionar, podemos volver a generarlo publicándolo de nuevo desde TFS.

Los datos del suscriptor de la Base de datos de SQL de Azure son el recurso más confidencial. Efectuamos una copia de seguridad de esto mediante la función integrada (consulte [Copia de seguridad y restablecimiento de Base de datos de SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La programación de la copia de seguridad establece una copia de seguridad completa de la base de datos una vez por semana, copias de seguridad diferenciales de la base de datos una vez al día y copias de seguridad del registro de transacciones cada 5 minutos. Dado el tamaño de los datos, esta solución es más que adecuada para nuestros volúmenes de datos inmediatos y proyectados.

En tercer lugar, almacenamos archivos de imagen y vídeo en el almacenamiento BLOB de Azure. Todavía estamos evaluando el plan de copia de seguridad final de estos datos, teniendo en cuenta el Explorador de Cloudberry de Azure como una posible solución. Por ahora, usamos un WebJob para copiar imágenes y vídeos en otra ubicación.

##¿Qué hemos aprendido?

Debido a que ya teníamos datos, resultaba fácil establecer la prueba de concepto. En horas, disponíamos de un prototipo con facetas de contadores, paginación, perfiles clasificados y resultados de búsqueda. Los resultados de búsqueda eran tan precisos que decidimos eliminar algunos de los filtros presentados al cliente final.

Lo que más nos sorprendió fue la rapidez con la que podíamos aprender el funcionamiento de Búsqueda de Azure, y todo lo que obteníamos a cambio. Literalmente, establecimos la prueba de concepto en unas pocas horas (consulte la nota facilitada a continuación), reemplazando 500 líneas de código por 3 líneas de código de la aplicación front-end (además de un nuevo WebJob nuevo) y obteniendo mejores resultados.

Anteriormente, nuestro código implementaba la paginación, recuentos y otros comportamientos que son estándar de la búsqueda. Mediante la Búsqueda de Azure, entre los resultados que obtenemos se incluyen las coincidencias de búsqueda, las facetas, los datos de paginación, los recuentos y todas las cosas que necesitábamos y teníamos que suministrarnos a nosotros mismos. También se incluye la potenciación y la navegación por facetas integrada, de la que no disponíamos en nuestra solución original.

El mayor desafío durante la implementación fue que era una versión preliminar y buscar información y experiencias compartidas resultaba difícil. En cuanto conectamos unos cuantos puntos, descubrimos que mediante el servicio de Búsqueda de Azure resultaba bastante sencillo debido a su formato de datos de la API de REST y JSON. Podíamos llamar al marco de trabajo directamente desde la mayoría de complementos de código abierto, como JQuery JSON.Net, y podíamos usar herramientas como Fiddler para obtener una depuración y experimentación rápidas.

> [AZURE.NOTE] Además de preparar los datos, ayudó a que los encargados de crear el prototipo entendiésemos cómo funciona la tecnología, lo cual nos permitió ser más productivos y apreciar más las funciones integradas. Si necesita reforzar la construcción de la consulta de búsqueda, la navegación por facetas, los filtros, etc., la creación de prototipos tardará más tiempo.

###Control de las facetas en la página de presentación de la búsqueda

Uno de los aspectos que aprendimos durante la prueba de concepto fue planear cuidadosamente por adelantado las facetas. Después de cargar una gran cantidad de datos en la solución, vimos que el volumen real de facetas era demasiado alto para presentar a los usuarios.

Resolvimos este problema mediante la restricción del parámetro de recuento de facetas. El parámetro count impone un límite en cuanto al número de facetas mostradas al usuario. [Aquí](search-faceted-navigation.md) encontrará un vínculo que incluirá una explicación sobre el parámetro count.

###WebJobs para tareas de programación

Búsqueda de Azure no fue la única agradable sorpresa para nosotros. Descubrimos que usar WebJobs para automatizar nuestras operaciones de carga de datos en la Búsqueda de Azure era considerablemente mejor a nuestro enfoque anterior, que implicaba el uso de una máquina virtual específica que dispusiese del programador de Windows, con las tareas programadas para actualizar el índice de búsqueda. WebJobs era más fácil de configurar y más fácil de depurar y, por supuesto, mucho más barato que una máquina virtual específica.

###Explorador de almacenamiento BLOB de Azure para actualizar imágenes

Nos dimos cuenta de que el uso del [Explorador de almacenamiento BLOB de Azure](https://azurestorageexplorer.codeplex.com/) (disponible en codeplex) resultó muy útil en la administración de actualizaciones de imagen y vídeo en el sitio. Se utiliza como una herramienta de desarrollo para actualizar manualmente las imágenes y vídeos que forman parte de nuestro sitio principal. Se ha determinado que es más flexible que implementar cambios en el portal y elimina una iteración de prueba completa cada vez que se necesita actualizar una imagen.

##Observaciones finales

Gracias a la gente excelente de WhatToPedia por permitirnos compartir su historia.

Esperamos que este caso práctico le haya resultado útil. Si usa la Búsqueda de Azure, le recomiendo algunos recursos para acelerar el proceso:

- [Foro MSDN dedicado a la Búsqueda de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow también tiene una etiqueta](http://stackoverflow.com/questions/tagged/azure-search)
- [Página de documentación de Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Documentación de Búsqueda de Azure en MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Apéndice: WebJob de indexador de búsqueda

El código siguiente genera el indexador mencionado en la sección sobre la creación del prototipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=AcomDC_0224_2016-->
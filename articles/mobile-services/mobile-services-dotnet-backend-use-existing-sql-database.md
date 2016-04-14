<properties
	pageTitle="Creación de un servicio usando una base de datos SQL existente con el backend .NET de Servicios móviles | Microsoft Azure"
	description="Obtener información acerca de cómo usar una base de datos SQL local o en la nube existente con su servicio móvil basado en .NET"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/09/2015"
	ms.author="glenga"/>


# Creación de un servicio usando una base de datos SQL existente con el backend .NET de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Con el backend .NET de Servicios móviles, es muy fácil aprovechar las ventajas que ofrecen recursos existentes para compilar un servicio móvil. Un escenario especialmente interesante es el uso de una base de datos SQL existente (local o en la nube), que ya pueden estar usando otras aplicaciones, para hacer que los datos existentes estén disponibles para clientes móviles. En este caso, es imprescindible que no se modifique el modelo de base de datos (o *esquema*) para que las soluciones actuales continúen funcionando.

<a name="ExistingModel"></a>
## Exploración del modelo de base de datos existente

Para este tutorial, usaremos la base de datos que se creó con su servicio móvil, pero no usaremos el modelo predeterminado que se crea, sino que crearemos manualmente un modelo arbitrario que representará una aplicación existente que pueda tener. Para obtener todos los detalles de cómo conectarse a una base de datos local, consulte [Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

1. Comience por crear un proyecto de servidor de Servicios móviles en **Visual Studio 2013 Update 2** o bien use el proyecto de inicio rápido que puede descargar en la pestaña Servicios móviles para el servicio en el [Portal de Azure clásico](http://manage.windowsazure.com). Para este tutorial, supondremos que el proyecto de servidor se denomina **ShoppingService**.

2. Cree un archivo denominado **Customer.cs** en la carpeta **Models** y use la siguiente implementación. Deberá agregar al proyecto una referencia de ensamblado a **System.ComponentModel.DataAnnotations**.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }

                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. Cree un archivo denominado **Order.cs** en la carpeta **Models** y use la siguiente implementación:

        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }

                public virtual Customer Customer { get; set; }

            }
        }

    Observará que estas dos clases tienen una *relación*: cada elemento **Order** está asociado con un único elemento **Customer** y un elemento **Customer** puede estar asociado con varios elementos **Order**. Tener relaciones es habitual en los modelos de datos existentes.

4. Cree un archivo denominado **ExistingContext.cs** en la carpeta **Models** e impleméntelo del siguiente modo:

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

Esta estructura es similar a un modelo de Entity Framework existente que quizá usa ya para alguna aplicación. Tenga en cuenta que el modelo aún no tiene conocimiento de Servicios móviles en esta etapa.

<a name="DTOs"></a>
## Creación de objetos de transferencia de datos (DTO) para el servicio móvil

El modelo de datos que le gustaría usar con el servicio móvil puede ser arbitrariamente complejo; podría contener cientos de entidades con una gran variedad de relaciones entre ellas. Cuando se compila una aplicación móvil, es preferible simplificar el modelo de datos y eliminar relaciones (o controlarlas manualmente) para minimizar la carga que se intercambia entre la aplicación y el servicio. En esta sección, crearemos un conjunto de objetos simplificados (conocidos como "objetos de transferencia de datos" o "DTO"), que se asignan a los datos que tiene en su base de datos, pero contienen solo las propiedades mínimas necesarias para la aplicación móvil.

1. Cree el archivo **MobileCustomer.cs** en la carpeta **DataObjects** del proyecto de servicio y use la siguiente implementación:

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Observe que esta clase es similar a la clase **Customer** del modelo, solo que se ha quitado la propiedad de relación de **Order**. Para que un objeto funcione correctamente con la sincronización sin conexión de Servicios móviles, necesita un conjunto de *propiedades del sistema* para la simultaneidad optimista. Por eso, notará que el objeto DTO se hereda de [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx), que contiene esas propiedades. La propiedad **CustomerId** basada en un valor int del modelo original se reemplaza por la propiedad **Id** basada en un valor de cadena de **EntityData**, que será el **Id** que usará Servicios móviles.

2. Cree el archivo **MobileOrder.cs** en la carpeta **DataObjects** del proyecto de servicio.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    La propiedad de relación de **Customer** se ha reemplazado por el nombre **Customer** y una propiedad **MobileCustomerId** que se puede usar para modelar manualmente la relación en el cliente. Por ahora, puede pasar por alto la propiedad **CustomerId**, solo se usa más adelante.

3. Quizá advierta que, al agregar las propiedades del sistema en la clase base **EntityData**, nuestros objetos DTO tienen ahora más propiedades que los tipos del modelo. Evidentemente, necesitamos un lugar para almacenar estas propiedades, por lo que agregaremos algunas columnas más a la base de datos original. Si bien esto modifica la base de datos, no interrumpe las aplicaciones existentes porque los cambios son meramente aditivos (se agregan nuevas columnas al esquema). Para ello, agregue las siguientes instrucciones al principio de **Customer.cs** y **Order.cs**:

        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System;

4. A continuación, agregue estas propiedades adicionales a cada una de las clases:

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. Las propiedades del sistema recién agregadas tienen algunos comportamientos integrados (por ejemplo, la actualización automática de "creado/actualizado a las") que tienen lugar de forma transparente con las operaciones de la base de datos. Para habilitar estos comportamientos, debemos hacer un cambio en **ExistingContext.cs**. Al principio del archivo, agregue lo siguiente:

        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

5. En el cuerpo de **ExistingContext**, invalide [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx):

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        }

5. Vamos a rellenar la base de datos con algunos datos de ejemplo. Abra el archivo **WebApiConfig.cs**. Cree un nuevo elemento [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx) y configúrelo en el método **Register** como se indica a continuación.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> {
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> {
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> {
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## Establecimiento de una asignación entre objetos DTO y el modelo

Ahora tenemos los tipos de modelo **Customer** y **Order**, y los objetos DTO **MobileCustomer** y **MobileOrder**, pero necesitamos indicarle al backend que los transforme automáticamente entre sí. Aquí, Servicios móviles se basa en [**AutoMapper**](http://automapper.org/), un asignador relacional de objetos al que ya se hace referencia en el proyecto.

1. Agregue el siguiente código al principio de **WebApiConfig.cs**:

        using AutoMapper;
        using ShoppingService.DataObjects;

2. Para definir la asignación, agregue el siguiente código al método **Register** de la clase **WebApiConfig**.

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper asignará ahora los objetos entre sí. Todas las propiedades con nombres coincidentes se combinarán; por ejemplo, **MobileOrder.CustomerId** se asignará automáticamente a **Order.CustomerId**. De este mismo modo, se pueden configurar asignaciones personalizadas donde se asigna la propiedad **MobileCustomerName** a la propiedad **Name** de la propiedad de relación de **Customer**.

<a name="DomainManager"></a>
## Implementación de lógica específica del dominio

El siguiente paso es implementar un elemento [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx), que actúa como capa de abstracción entre nuestro almacén de datos asignados y el controlador que atenderá el tráfico HTTP de nuestros clientes. Podremos escribir nuestro controlador en la sección siguiente puramente en términos de objetos DTO y el elemento **MappedEntityDomainManager** que agregamos aquí controlará la comunicación con el almacén de datos original, a la vez que nos proporciona un lugar para implementar cualquier lógica específica para él.

1. Agregar una **MobileCustomerDomainManager.cs** a la carpeta **Models** del proyecto. Pegue la siguiente implementación:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }

                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    Una parte importante de esta clase es el método **GetKey**, donde indicamos cómo ubicar la propiedad ID del objeto en el modelo de datos original.

2. Agregue un **MobileCustomerDomainManager.cs** a la carpeta **Models** del proyecto:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    En este caso, los métodos **InsertAsync** y **UpdateAsync** son interesantes. Aquí es donde imponemos la relación de que cada **Order** debe tener asociado un **Customer** válido. En **InsertAsync**, verá que rellenamos la propiedad **MobileOrder.CustomerId**, que se asigna a la propiedad **Order.CustomerId**. Obtenemos ese valor buscando el valor **Customer** con el valor **MobileOrder.MobileCustomerId** coincidente. Esto se debe a que, de forma predeterminada, el cliente solo conoce el identificador de Servicios móviles (**MobileOrder.MobileCustomerId**) del **Customer**, que es diferente de su clave principal real necesaria para establecer la clave externa (**MobileOrder.CustomerId**) de **Order** en **Customer**. Esto se usa solo internamente en el servicio para facilitar la operación de inserción.

Ya estamos preparados para crear controladores para exponer nuestros objetos DTO a nuestros clientes.

<a name="Controller"></a>
## Implementación de un elemento TableController con objetos DTO

1. En la carpeta **Controllers**, agregue el archivo **MobileCustomerController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    Notará el uso del atributo AuthorizeLevel para restringir el acceso público a las operaciones Insert/Update/Delete en el controlador. Para este escenario, la lista de Customers será de solo lectura, pero permitiremos la creación de nuevos Orders y su asociación con Customers existentes.

2. En la carpeta **Controllers**, agregue el archivo **MobileOrderController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. Ahora ya puede ejecutar el servicio. Presione **F5** y use el cliente de prueba integrado en la página de ayuda para modificar los datos.

Observe que ambas implementaciones de controlador hacen un uso exclusivo de los objetos DTO **MobileCustomer** y **MobileOrder**, y que no tienen conocimiento del modelo subyacente. Estos DTO se serializan fácilmente en JSON y se pueden usar para intercambiar datos con el SDK de cliente de Servicios móviles en todas las plataformas. Por ejemplo, si se compilase una aplicación de la Tienda Windows, el tipo del lado cliente correspondiente tendría el siguiente aspecto. El tipo sería análogo en otras plataformas de cliente.

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }

            [Version]
            public string Version { get; set; }

        }

    }

Para continuar, ya puede compilar la aplicación de cliente para acceder al servicio.

<!---HONumber=AcomDC_1203_2015-->
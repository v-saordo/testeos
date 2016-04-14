
Ahora, actualicemos la aplicación para almacenar elementos en los Servicios móviles de Azure en lugar de en la colección de la memoria local.

* En **TodoService.h**, busque la línea siguiente:

```
// TODO - create an MSClient property
```

Reemplace este comentario por la siguiente línea. De esta manera se crea una propiedad que representa el `MSClient` que se conecta al servicio.

```
@property (nonatomic, strong)   MSClient *client;
```


* En **TodoService.m**, busque la línea siguiente:

```
// TODO - create an MSTable property for your items
```

Reemplace este comentario por la siguiente línea dentro de la declaración `@interface`. Así se crea una representación de propiedad para su tabla de servicios móviles.

```
@property (nonatomic, strong)   MSTable *table;
```


* En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y elija el servicio móvil. Haga clic en la pestaña **Panel** y anote el valor de la dirección **URL del sitio**. A continuación, haga clic en **Administrar claves** y tome nota de la **Clave de la aplicación**. Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.


* En **TodoService.m**, busque la línea siguiente:

```
// Initialize the Mobile Service client with your URL and key.
```

Después de este comentario, agregue la siguiente línea de código: Reemplace `APPURL` y `APPKEY` por la dirección URL del sitio y la clave de la aplicación obtenida en el paso anterior.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* En **TodoService.m**, busque la línea siguiente:

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Reemplace este comentario por la siguiente línea. De esta manera se crea la instancia de la tabla TodoItem.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* En **TodoService.m**, busque la línea siguiente:

```
// Create a predicate that finds items where complete is false
```

Reemplace este comentario por la siguiente línea. Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Busque la línea siguiente:

```
// Query the TodoItem table and update the items property with the results from the service
```

Reemplace el comentario y la invocación del bloque de **finalización** posterior por el siguiente código:

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Localice el método **addItem** y reemplace su cuerpo por el siguiente código: Este código envía una solicitud de inserción al servicio móvil.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* Busque el método **completeItem** y busque la siguiente línea de código comentada:

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Reemplace el cuerpo del método, desde ese punto hasta el final del método, con el código siguiente: Este código quita los elementos todo una vez que se han marcado como completados.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* En TodoListController.m, busque el método **onAdd** y sobrescríbalo con el siguiente código:

```
- (IBAction)onAdd:(id)sender
{
    if (itemText.text.length  == 0)
    {
        return;
    }

    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
    UITableView *view = self.tableView;
    [self.todoService addItem:item completion:^(NSUInteger index)
    {
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        [view insertRowsAtIndexPaths:@[ indexPath ]
                    withRowAnimation:UITableViewRowAnimationTop];
    }];

    itemText.text = @"";
}
```

<!---HONumber=AcomDC_1203_2015-->
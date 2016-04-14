<properties
	pageTitle="Gestión de conflictos con datos sin conexión en Servicios móviles (iOS) | Centro de desarrollo móvil"
	description="Aprenda a usar Servicios móviles de Azure para gestionar los conflictos que se producen al sincronizar datos sin conexión en su aplicación iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="krisragh;donnam"/>


# Control de conflictos con la sincronización de datos sin conexión en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure. Este tutorial se basa en el tutorial [Introducción a los datos sin conexión].

>[AZURE.NOTE]Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>.


## Descargar el proyecto de iOS

Para este tutorial, descargue [un proyecto de Xcode actualizado desde Github](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS). Hemos usado el proyecto de Xcode desde el final del tutorial [Introducción a los datos sin conexión] como punto de partida y luego lo hemos actualizado para permitir la edición de elementos. También hemos agregado métodos y clases auxiliares, por lo que podemos agregar un controlador de conflictos en la sección siguiente.

Al final de este tutorial, si ejecuta esta aplicación en dos teléfonos, cambia el mismo elemento en ambos teléfonos localmente e inserte los cambios de nuevo en el servidor. Al hacerlo, permitirá al usuario en cada teléfono elegir qué versión quiere conservar: * mantener la versión del cliente (que reemplaza la versión del servidor), * mantener la versión del servidor (que actualiza la tabla local del cliente) o *no mantener ninguna versión (cancela la inserción y deja la operación pendiente).

Ahora, vamos a agregar el controlador de conflictos para habilitar esta capacidad.

## <a name="add-conflict-handling"></a>Incorporación de un controlador de conflictos al controlador de vista de lista Todo

1. En **QSTodoListViewController.m**, edite **viewDidLoad**. Reemplace la llamada a **defaultService** por una llamada a **defaultServiceWithDelegate** en su lugar:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. En **QSTodoListViewController.h**, agregue **&lt;MSSyncContextDelegate&gt;** a la declaración de la interfaz para que podamos implementar el protocolo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Agregue la siguiente instrucción de importación en la parte superior de **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Finalmente, vamos a agregar las dos operaciones siguientes a **QSTodoListViewController.m** para usar esta clase auxiliar y pedir al usuario que resuelva el conflicto de una de tres maneras.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

## <a name="test-app"></a>Prueba de la aplicación

Vamos a probar la aplicación con conflictos. Edite el mismo elemento en dos instancias distintas de la aplicación que se ejecuten al mismo tiempo, o con la aplicación y un cliente REST.

Realice el gesto de actualizar en las instancias de la aplicación arrastrando desde la parte superior. Ahora verá un símbolo del sistema para resolver el conflicto:

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Introducción a los datos sin conexión]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_1223_2015-->
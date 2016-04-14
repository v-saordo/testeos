<properties
   pageTitle="Prueba de la oferta del Servicio de datos para Marketplace | Microsoft Azure"
   description="Obtenga información acerca de cómo probar la oferta del Servicio de datos para Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="hascipio; avikova" />

# Probar su oferta de Servicio de datos en Ensayo
Después de completar los dos primeros pasos de [Creación de la cuenta de desarrollador de Microsoft](marketplace-publishing-accounts-creation-registration.md) y [Creación de la oferta de servicio de datos en el portal de publicación](marketplace-publishing-data-service-creation.md), está listo para realizar la oferta disponible en Azure Marketplace. Este tema le guiará a través del primer paso intermedio denominado "Almacenamiento provisional"

En la etapa de ensayo se implementa la oferta en un “espacio aislado” privado, donde puede probar y comprobar su funcionalidad antes pasarla a producción. La oferta aparecerá en un entorno de ensayo tal y como lo haría para un cliente que la implementase.

## Paso 1. Traslado de su oferta a un almacenamiento provisional
El traslado de la oferta a un almacenamiento provisional permite probar la oferta antes de que esté disponible para suscriptores futuros. Puede ver cómo aparecerá y funcionará la oferta para aquellos que se suscriban a sus datos.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.	Inicie sesión en el [Portal de publicación](https://publish.windowsazure.com).
2.	Seleccione **Servicios de datos** en la ventana de navegación izquierda
3.	Seleccione la oferta que desee trasladar al almacenamiento provisional. Verá la pantalla anterior.
4.	Haga clic en el botón **Trasladar a almacenamiento provisional**.  
5.	Si hay problemas con la oferta que deban completarse antes del traslado al almacenamiento provisional, podrá ver una lista. Para corregir estos elementos, haga clic en cada elemento de la lista. Cuando se hayan efectuado todas las correcciones, haga clic en el botón **Trasladar a almacenamiento provisional** nuevamente.

Si no hay ningún problema con su oferta, se visualizará la ventana emergente que aparece a continuación.

Si no ha planeado o no está autorizado a exponer su oferta en el Portal de Azure (actualmente dispone de una capacidad limitada), simplemente cierre la ventana emergente.

Para probar el Servicio de datos en el Portal de Azure (además del portal de DataMarket), necesitará un identificador de una suscripción de Azure con el que efectuar la prueba. Este identificador de suscripción identificará la cuenta que podrá probar su oferta.

Corte y pegue el identificador de suscripción y haga clic en la marca de verificación para continuar.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE]Estos identificadores de suscripciones a Azure solo son necesarios para las pruebas y el almacenamiento provisional en el [Portal de administración de Azure](https://manage.windowsazure.com). No se requieren para efectuar pruebas en Azure Marketplace.

En la siguiente pantalla que aparece se muestra que se está efectuando la publicación, mostrando el icono "En curso" resaltado en amarillo a continuación. La inserción en el almacenamiento provisional dura entre 10 y 15 minutos. Si tarda más, primero actualice su explorador (presione F5 en Internet Explorer). En los casos poco frecuentes en los que la oferta todavía se insertando en el almacenamiento provisional después de una hora, haga clic en el vínculo Póngase en contacto con nosotros para hacernos saber que hay un problema.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Cuando finalice la inserción en el almacenamiento provisional, el icono "En curso" dejará de moverse y el estado se actualizará a "Almacenado provisionalmente". Ahora ya estará preparado para probar su oferta.

## Paso 2: Probar su oferta almacenada provisionalmente en DataMarket

Haga clic en el vínculo que aparece detrás del texto **"Ver su oferta de servicio en..."** para mostrar la pantalla que verá el suscriptor cuando la oferta pase a producción y aparecerá en DataMarket.

  ![dibujo](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Pruebe o verifique cada uno de los 12 elementos marcados anteriormente para asegurarse de que todos los logotipos, los precios, las transacciones, el texto, las imágenes, la documentación y los vínculos son correctas y funcionan correctamente. Se trata de un buen momento para asegurarse de que los valores de prueba introducidos al crear la oferta se han reemplazado por valores reales.

1. Logotipo de la oferta
2. Nombre de la oferta
3. Nombre del publicador/vínculo al sitio web de su compañía
4. Categorías de búsqueda para su oferta
5. Vínculo de soporte técnico de su oferta para ayudar a los suscriptores
6. Descripción contextual de la oferta
7. Plan de oferta que describe los detalles de la facturación
8. Vínculo al código de implementación
9. Imágenes de ejemplo que ilustran el uso de los datos de la oferta
10. Metadatos de entrada y salida para cada servicio de la oferta
11. Términos de uso de la oferta
12. Vista previa de los datos de la oferta


Por último, compruebe que el servicio funcione a través de Datamarket haciendo clic en el vínculo "EXPLORAR ESTE CONJUNTO DE DATOS". Se abrirá una nueva ventana de la herramienta llamada "Explorador de servicios" para que pueda obtener una vista previa de los resultados de una consulta a su servicio. En esta ventana, puede especificar los parámetros necesarios y ver los resultados que aparecen en una consulta a su servicio. También se muestra la dirección URL de la consulta.

> [AZURE.NOTE]Asegúrese de revisar la descripción textual del servicio que se muestra en la parte superior. Y si su oferta consta de más de una llamada de servicio, haga clic en las pestañas de la parte inferior para pasar al siguiente servicio para revisar y probar.



## Paso siguiente
Si está teniendo problemas y necesita ayuda para resolverlos, póngase en contacto con el [soporte técnico para publicadores de Azure](http://go.microsoft.com/fwlink/?LinkId=272975).

Si está satisfecho y listo para publicar su oferta, lea la documentación [Solicitud de aprobación para la inserción en producción](marketplace-publishing-push-to-production.md).

## Otras referencias
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0114_2016-->
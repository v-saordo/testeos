<properties 
	pageTitle="Cómo agregar unidades de codificación" 
	description="Información sobre cómo agregar unidades de codificación con .NET"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako,milangada,gtrifonov" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016"
	ms.author="juliako"/>


#Escalado de codificación con el SDK de .NET


> [AZURE.SELECTOR]
- [Portal](media-services-portal-encoding-units.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Información general

Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan los trabajos de codificación. Puede elegir uno de los siguientes tipos de unidad reservada: S1, S2 o S3. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada Standard en comparación con el tipo Basic. Para obtener más información, consulte el blog acerca de los tipos de unidades reservadas de codificación escrito por [Milan Gada](https://azure.microsoft.com/blog/author/milanga/).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas multimedia que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente tan pronto como finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación mediante el SDK de .NET, haga lo siguiente:

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##Apertura de una incidencia de soporte técnico

De manera predeterminada las cuentas de Servicios multimedia pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

###Abrir una incidencia de soporte técnico

Para abrir una incidencia de soporte técnico, haga lo siguiente:

1. Haga clic en [Obtener soporte técnico](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le solicitará que especifique sus credenciales.

1. Seleccione su suscripción.
 
1. En el tipo de soporte, seleccione "Técnico".
 
1. Haga clic en "Crear incidencia".
 
1. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.
 
1. Seleccione un "Tipo de problema" que sea pertinente en relación con su problema.
 
1. Haga clic en Continue.
 
1. Siga las instrucciones que aparecen en la página siguiente y, a continuación, escriba los detalles de su problema.
 
1. Haga clic en Submit para abrir la incidencia.
 


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->
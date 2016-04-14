<properties
	pageTitle="Escalación del procesamiento multimedia mediante el Portal de Azure clásico"
	description="Aprenda a escalar Servicios multimedia mediante la especificación del número de unidades reservadas de streaming a petición y unidades reservadas de codificación con las que desea aprovisionar la cuenta."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
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


# Escalación del procesamiento multimedia mediante el Portal de Azure clásico

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Información general

Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo **S1**. Para obtener más información, consulte el blog [Encoding Reserved Unit Types](https://azure.microsoft.com/blog/author/milanga/) (Codificación de tipos de unidad reservada).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas multimedia que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente tan pronto como finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

>[AZURE.IMPORTANT] Las unidades reservadas sirven para establecer paralelismos en todo el procesamiento multimedia, incluida la indexación de trabajos mediante Azure Media Indexer. Sin embargo, a diferencia de la codificación, la indexación de los trabajos no se procesará más rápido con unidades reservadas de mayor rapidez.

Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación, haga lo siguiente:

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página **CODIFICACIÓN**.

	Para cambiar el **TIPO DE UNIDAD RESERVADA**, presione S1, S2 o S3.

	Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **CODIFICACIÓN**.


	![Página de procesadores](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)


	>[AZURE.NOTE] Los siguientes centros de datos no ofrecen el tipo de unidad reservada Premium: Singapur, Hong Kong, Osaka, Beijing, Shanghai.

3. Presione el botón SAVE para guardar los cambios.

	Las nuevas unidades reservadas de codificación se asignan en cuanto presiona GUARDAR.

	>[AZURE.NOTE] Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste.

##Cuotas y limitaciones

Para obtener información sobre las cuotas y limitaciones y sobre cómo abrir una incidencia de soporte técnico, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->
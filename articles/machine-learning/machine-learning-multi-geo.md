<properties
   pageTitle="Documentación de ayuda multigeográfica | Microsoft Azure"
   description="Aprenda a crear un área de trabajo y a publicar un servicio web en una región de Azure diferente de la región central del sur de Estados Unidos (SCUS) de Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="tedway; neerajkh"/>

# Documentación de ayuda multigeográfica

En este artículo se describe cómo crear un área de trabajo y publicar un servicio web en otras regiones de Azure.

## Crear un área de trabajo

1. Inicie sesión en el Portal de Azure clásico.

2.  Haga clic en **+ NUEVO** > **SERVICIOS DE DATOS** > **APRENDIZAJE AUTOMÁTICO** > **CREACIÓN RÁPIDA**. En **UBICACIÓN**, seleccione otra región, como **Sudeste de Asia**. ![Imagen de ayuda multigeográfica 1][1]
3. Seleccione el área de trabajo y, a continuación, haga clic en **Inicio de sesión en Estudio de aprendizaje automático**. ![Imagen de ayuda multigeográfica 2][2]

4. Ahora dispone de un área de trabajo en otra región que se puede usar como cualquier otra área de trabajo. Para cambiar entre las áreas de trabajo, busque la esquina superior derecha de la pantalla. Haga clic en la lista desplegable, seleccione la región y, a continuación, seleccione el área de trabajo. Todo es local para la región del área de trabajo; por ejemplo, todos los servicios web creados a partir de un área de trabajo estarán en la misma región en la que se encuentra el área de trabajo. ![Imagen de ayuda multigeográfica 3][3]

## Abrir un experimento de Galería

Si abre un experimento de Galería, también puede seleccionar en qué región desea copiar el experimento.

![Imagen de ayuda multigeográfica 4][4a]

## Administración de servicios web

Para administrar mediante programación los servicios web, como el reciclaje, use la dirección específica de la región: ****https://asiasoutheast.management.azureml.net**

### Puntos a tener en cuenta

1.	Solo se pueden copiar experimentos entre áreas de trabajo que pertenecen a la misma región. En el futuro, habilitaremos la copia de experimentos entre áreas de trabajo en varias regiones.
2.	El selector de región solo mostrará áreas de trabajo de una región de cada vez. En el futuro, podrá ver una lista completa de las áreas de trabajo a las que tiene acceso en todas las regiones al mismo tiempo.  
3.	Se creará un área de trabajo libre o un área de trabajo (anónima) de acceso a invitados y se alojará en la zona central del sur de EE. UU. En el futuro, podrá crear áreas de trabajo de acceso de invitado/libre en la región que elija.  
4.	Los servicios web implementados desde un área de trabajo del sudeste asiático también se hospedarán en el sudeste asiático. En el futuro, podrá tener la flexibilidad de crear experimentos en una región e implementar genera extremos de servicios web generados en diferentes regiones.  

## Más información

Formule una pregunta en el [Foro de Aprendizaje automático de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=AcomDC_0218_2016-->
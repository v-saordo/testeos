<properties 
	pageTitle="Creación de un área de trabajo de aprendizaje automático | Microsoft Azure" 
	description="Creación de un área de trabajo para Estudio de aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="garye;bradsev"/>


# Creación de un área de trabajo de Aprendizaje automático de Azure 

Este menú vincula a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos usados por el proceso de análisis de Cortana (CAPS).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Para usar Estudio de aprendizaje automático de Azure, debe tener un área de trabajo de Aprendizaje automático. Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Para crear un área de trabajo

1. Inicie sesión en el [Portal de Microsoft Azure clásico](https://manage.windowsazure.com/).
2. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.

    ![Servicio de Aprendizaje automático][1]

3. Haga clic en **+NUEVO** en la parte inferior de la ventana.
4. Haga clic en **SERVICIOS DE DATOS**, luego en **APRENDIZAJE AUTOMÁTICO** y finalmente en **CREACIÓN RÁPIDA**.

	![Creación rápida de una nueva área de trabajo][3]

5. Escriba un **NOMBRE DE ÁREA DE TRABAJO** para el área de trabajo y especifique el **PROPIETARIO DE ÁREA DE TRABAJO**. El propietario del área de trabajo debe ser una cuenta Microsoft válida (por ejemplo, name@outlook.com)).

    > [AZURE.NOTE] Más tarde puede compartir los experimentos en los que trabaja invitando a otros a su área de trabajo. Para ello, en Estudio de aprendizaje automático, vaya a la página **CONFIGURACIÓN**. Solo necesita la cuenta Microsoft o la cuenta de organización de cada usuario.

6. Especifique la **UBICACIÓN** de Azure, luego escriba una **CUENTA DE ALMACENAMIENTO** de Azure existente o seleccione **Crear una nueva cuenta de almacenamiento** para crear una nueva.
7. Haga clic en **CREAR UN ÁREA DE TRABAJO DE ML**.

Después de crear el área de trabajo del Aprendizaje automático, verá que aparece en la página **Aprendizaje automático**.

Para obtener más información sobre cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure]. Si encuentra un problema al crear el área de trabajo, vea [Guía de solución de problemas: creación y conexión a un área de trabajo de Aprendizaje automático].

[Administración de un área de trabajo de Aprendizaje automático de Azure]: machine-learning-manage-workspace.md
[Guía de solución de problemas: creación y conexión a un área de trabajo de Aprendizaje automático]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=AcomDC_0204_2016-->
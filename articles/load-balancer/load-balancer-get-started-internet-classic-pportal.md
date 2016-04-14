<properties 
   pageTitle="Introducción a la creación de un equilibrador de carga orientado a Internet en un modelo de implementación clásica con el portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el modelo de implementación clásica mediante el portal de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet (clásico) en el portal de Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

 
[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Introducción a la creación de un punto de conexión del equilibrador de carga mediante el Portal de Azure	

Para crear un modelo de implementación de equilibrador de carga accesible desde Internet (clásica) desde el Portal de Azure, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.

2. Vaya a la hoja de máquinas virtuales (modelo clásico) y seleccione una máquina virtual.

3. En la hoja "Elementos esenciales" de las máquinas virtuales, seleccione "Todas las configuraciones".

4. Haga clic en "Conjuntos de carga equilibrada".

5. Para crear un nuevo equilibrador de carga, haga clic en el icono "Unirse" en la parte superior de la hoja de conjuntos de carga equilibrada.

6. Seleccione el tipo público de "conjunto de carga equilibrada" para el equilibrador de carga orientado a Internet.

7. Haga clic en "Configurar los valores obligatorios" para abrir "Elegir un conjunto de carga equilibrada" y haga clic en "Crear un conjunto de carga equilibrada".

8. En la hoja "Crear un conjunto de carga equilibrada", cree un nombre para el conjunto de carga equilibrada. Rellene el nombre, el puerto público, el protocolo de sondeo y el puerto de sondeo.

9. Si es necesario, cambie el intervalo de sondeo y los reintentos.

10. (Opcional) Si lo desea, puede configurar reglas de control de acceso desde la hoja de creación del conjunto de carga equilibrada.

11. Haga clic en Aceptar para volver a la hoja "Unirse al conjunto de carga equilibrada".

12. Haga clic en Aceptar y espere a que el nuevo recurso de carga equilibrada aparezca en la hoja "Conjuntos de carga equilibrada".
 
## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1210_2015-->
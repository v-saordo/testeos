<properties
   pageTitle="Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local | Microsoft Azure"
   description="Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="01/30/2016"
   ms.author="tarcher" />


# Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local

Con Emulator Express, puede probar y depurar un servicio en la nube sin ejecutar Visual Studio como administrador. Puede establecer la configuración del proyecto para usar Emulator Express o el emulador completo, según los requisitos de su servicio en la nube. Para obtener más información sobre el emulador completo, consulte [Ejecutar una aplicación de Azure en el emulador de proceso](/storage/storage-use-emulator.md). Emulator Express se incluyó primero en el SDK de Azure 2.1 y, a partir del SDK de Azure 2.3, se ha convertido en el emulador predeterminado.

## Uso de Emulator Express en el IDE de Visual Studio

Al crear un nuevo proyecto en el SDK de Azure 2.3 o posterior, Emulator Express ya está seleccionado. En los proyectos existentes que se crearon con una versión anterior del SDK, siga estos pasos para seleccionar Emulator Express.

### Para configurar un proyecto para usar Emulator Express

1. En el menú contextual del proyecto de Azure, elija **Propiedades**, y, a continuación, elija la pestaña **Web**.

1. En **Servidor de desarrollo local**, elija el botón de opción **Usar IIS Express**. Emulator Express no es compatible con el servidor web de IIS.

1. En **Emulador**, elija el botón de opción **Usar Emulator Express**.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Inicio de Emulator Express en un símbolo del sistema

En un símbolo del sistema, puede iniciar la versión Express del emulador de proceso de Azure, csrun.exe, con la opción /useemulatorexpress.

## Limitaciones

Antes de usar Emulator Express, debe tener en cuenta algunas limitaciones:

- Emulator Express no es compatible con el servidor web de IIS.

- Su servicio en la nube puede contener varios roles, pero cada rol se limita a una instancia.

- No puede tener acceso a los números de puerto inferiores a 1000. Por ejemplo, si utiliza un proveedor de autenticación que suele usar un puerto inferior a 1000, puede que tenga que cambiar este valor a un número de puerto superior a 1000.

- Las limitaciones que se aplican al emulador de proceso de Azure se aplican también a Emulator Express. Por ejemplo, no puede tener más de 50 instancias de rol por implementación. Consulte [Ejecutar una aplicación de Azure en el emulador de proceso](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## Pasos siguientes

[Depuración de Servicios en la nube](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=AcomDC_0204_2016-->
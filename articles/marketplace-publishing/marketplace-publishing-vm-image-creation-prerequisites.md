<properties
   pageTitle="Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace | Microsoft Azure"
   description="Entienda los requisitos para crear e implementar una imagen de máquina virtual en Azure Marketplace para que otros usuarios la compren."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="02/04/2016"
  ms.author="hascipio; v-divte"/>

# Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace
Lea el proceso minuciosamente antes de empezar y comprenda dónde y por qué se realiza cada paso. Tanto como sea posible, debe preparar la información de su compañía y otros datos, descargar las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta. Estos elementos deben estar claros tras la revisión de este artículo.

## Descargar herramientas y aplicaciones necesarias
Debe tener listos los elementos siguientes antes de comenzar el proceso:

- Dependiendo de su sistema operativo objetivo, instale los cmdlets de Azure PowerShell o la herramienta de la interfaz de línea de comandos de Linux desde la página de descargas de Azure.
- Instale el Explorador de almacenamiento de Azure desde CodePlex.
- Descargue e instale la herramienta Certification Test Tool for Azure Certified:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Necesita un equipo basado en Windows para ejecutar la herramienta de certificación. Si no tiene un equipo basado en Windows disponible, puede ejecutar la herramienta con una VM basada en Windows en Azure.

## Plataformas compatibles
Puede desarrollar VM basadas en Azure en Windows o Linux. Algunos elementos del proceso de publicación, como la creación de un disco duro virtual compatible con Azure, usan diferentes herramientas y pasos según el sistema operativo que usa:

- Si usa Linux, consulte la sección "Creación de un VHD compatible con Azure (basado en Linux)" de la [Virtual machine image publishing guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual).
- Si usa Windows, consulte la sección "Creación de un VHD compatible con Azure (basado en Windows)" de la [Virtual machine image publishing guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual).

> [AZURE.NOTE] Necesita tener acceso a un equipo de Windows para: - Ejecutar la herramienta de validación de certificación. - Crear la dirección URL de firma de acceso compartido del disco duro virtual para el envío de certificación del disco duro virtual.

## Desarrollar el disco duro virtual
Puede desarrollar discos duros virtuales de Azure en la nube o de forma local:

- El desarrollo basado en la nube significa que todos los pasos de desarrollo se realizan de forma remota en un disco duro virtual residente en Azure.
- Para el desarrollo local se requiere la descarga de un disco duro virtual y su desarrollo mediante infraestructura local. Aunque esto es posible, no lo recomendamos. Tenga en cuenta que para el desarrollo para Windows o SQL de forma local se requiere que tenga las claves de licencia local pertinentes. No puede incluir ni instalar SQL Server tras crear una máquina virtual. También debe basar su oferta en una imagen SQL aprobada del portal de Azure. Si decide desarrollar de forma local, debe realizar algunos pasos de forma diferente a si desarrollara en la nube. Encontrará información pertinente en [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md).

## Pasos siguientes
Ahora que ha revisado los requisitos previos y completado la tarea necesaria, puede continuar con la creación de su oferta de imagen de máquina virtual como se detalla en [Virtual machine image publishing guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual).

## Consulte también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_0211_2016-->
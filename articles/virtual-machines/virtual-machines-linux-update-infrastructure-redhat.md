<properties
   pageTitle="Una infraestructura de actualización para imágenes de Red Hat Enterprise Linux | Microsoft Azure"
   description="Presenta el servicio de actualización de yum para una instancia de Red Hat Enterprise Linux a petición en Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# Una infraestructura de actualización para imágenes de Red Hat Enterprise Linux

Mediante Red Hat Update Infrastructure (RHUI) en Azure, puede administrar el contenido del repositorio yum para imágenes de Red Hat Enterprise Linux (RHEL) de Azure. Las instancias RHEL a petición que se crearon desde Azure Marketplace tendrán entonces acceso al repositorio yum regional. Esto permite que las instancias RHEL reciban actualizaciones incrementales.

La lista del repositorio yum, que se administra mediante RHUI, se configura en la instancia de RHEL durante el aprovisionamiento. Por ello, no es necesario realizar ninguna configuración adicional: solo tiene que ejecutar `yum update` una vez que la instancia de RHEL se esté ejecutando.

## Introducción sobre RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ofrece una solución altamente escalable para administrar el contenido del repositorio yum para las instancias en la nube de Red Hat Enterprise Linux hospedadas por proveedores de nube con certificación de Red Hat. Basado en el proyecto Pulp ascendente, RHUI permite a los proveedores de nube reflejar localmente el contenido del repositorio hospedado en Red Hat, crear repositorios personalizados con su propio contenido y poner los repositorios a disposición de un grupo grande de usuarios finales a través de un sistema de entrega de contenido de carga equilibrada.

## Regiones donde se implementa RHUI
RHUI se implementa en todas las regiones públicas que se muestran en el [panel de estado de Azure](https://azure.microsoft.com/status/). Esto significa que puede obtener el servicio de actualización de yum sin ningún cargo adicional en estas regiones. Esta información se actualizará en el futuro.

## Obtención de actualizaciones desde un repositorio de actualización local (por ejemplo, Red Hat Network Satellite)

Para obtener actualizaciones desde un repositorio de actualización local, debe tener una licencia de Red Hat Cloud Access y una suscripción existente de Red Hat en Azure.

A continuación, debe anular el registro de RHUI y volver a registrarse en su infraestructura de actualización local. A continuación, se muestran los pasos detallados.

1.	Edite /etc/yum.repos.d/rh-cloud.repo y cambie todas las apariciones de `enabled=1` por `enabled=0`. Por ejemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Edite /etc/yum/pluginconf.d/rhnplugin.conf y cambie `enabled=0` por `enabled=1`.
3.	A continuación, regístrese en Red Hat Network (RHN).

        rhn_register

    o

        rhnreg_ks


> [AZURE.NOTE] Las transferencias de datos salientes se cobrarán (consulte los [detalles sobre el precio](http://azure.microsoft.com/pricing/details/data-transfers/)). Se recomienda utilizar RHUI de forma predeterminada para obtener actualizaciones incrementales sin incurrir en gastos adicionales.

## Pasos siguientes
Ahora que entiende el funcionamiento de RHUI en Azure, puede crear una imagen de RHEL desde [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) y usar `yum update` en su instancia de RHEL.

<!---HONumber=AcomDC_0224_2016-->
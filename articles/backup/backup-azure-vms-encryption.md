<properties
   pageTitle="Copia de seguridad de Azure: copia de seguridad de máquinas virtuales de IaaS de Azure con discos cifrados | Microsoft Azure"
   description="Averigüe cómo Copia de seguridad de Azure controla los datos cifrados con BitLocker o dmcrypt durante la copia de seguridad de máquinas virtuales de IaaS. Este artículo le prepara para las diferencias en las experiencias de copia de seguridad y restauración al trabajar con discos cifrados."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="11/27/2015"
   ms.author="aashishr"/>

# Tratar con discos cifrados durante la copia de seguridad de máquina virtual

Para las empresas que desean cifrar sus datos de máquina virtual en Azure, la solución que se debe usar es Bitlocker en Windows o dmcrypt en equipos Linux. Ambas son soluciones de cifrado de nivel de volumen. En este artículo se trata con los aspectos específicos de configuración de la copia de seguridad para dichas máquinas virtuales de Azure y el impacto en los flujos de trabajo de restauración debido a los datos cifrados.

## Cómo funciona la copia de seguridad

La solución consta de dos niveles: el nivel de máquina virtual y el nivel de almacenamiento.

1. El nivel de máquina virtual trata con los datos como se ven por el sistema operativo invitado y las aplicaciones que se ejecutan en la máquina virtual. También es la capa que ejecuta el software de cifrado (Bitlocker o dmcrypt), cifrando de forma transparente datos en los volúmenes antes de escribir los datos en los discos.
2. La capa de almacenamiento se ocupa de los blobs de páginas y de los discos conectados a la máquina virtual. No conoce los datos que se escriben en el disco, y si está cifrado o no. Se trata de la capa en la que opera la funcionalidad de copia de seguridad de la máquina virtual.

![Cómo coexisten el cifrado de Bitlocker y la copia de seguridad de máquinas virtuales de Azure](./media/backup-azure-vms-encryption/how-it-works.png)

El cifrado completo de datos se produce de forma transparente y sin problemas en el nivel de máquina virtual. Por tanto, los datos escritos en los blobs de página conectados a la máquina virtual son los datos cifrados. Cuando la [Copia de seguridad de Azure toma una instantánea de los discos de la máquina virtual y transfiere los datos](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), copia los datos cifrados presentes en los blobs de página.

## Componentes de soluciones

Hay muchos elementos de esta solución que se tienen que configurar y administrar correctamente para que funcione:

| Función | Software usado | Notas adicionales |
| -------- | ------------- | ------- |
| Cifrado | BitLocker o dmcrypt | Como el cifrado se produce en una capa *diferentes* en comparación con la Copia de seguridad de Azure, no importa qué software de cifrado se usa. Dicho eso, esta experiencia se ha validado solo con Bitlocker y dmcrypt.<br><br> Para cifrar los datos, se necesita una clave. La clave también se tiene que mantener segura para garantizar el acceso autorizado a los datos. |
| Administración de claves | CloudLink SecureVM<br>o KeyVault de Azure | La clave es esencial para cifrar o descifrar los datos. Sin la clave correcta, no se pueden recuperar los datos. Esto resulta *increíblemente* importante con:<br><li>Sustituciones clave<li>Retención a largo plazo<br><br>Por ejemplo, es posible que la clave usada para hacer copias de seguridad de datos hace 7 años no sea la misma clave que se usa en la actualidad. Sin la clave de hace 7 años, será imposible usar los datos restaurados de ese momento.|
| Copia de seguridad de datos | Copia de seguridad de Azure | Use la Copia de seguridad de Azure para hacer una copia de seguridad de una de las máquinas virtuales de IaaS de Azure a través del [portal de administración de Azure](http://manage.windowsazure.com) o con PowerShell |
| Restauración de datos | Copia de seguridad de Azure | Use la Copia de seguridad de Azure para restaurar discos o una máquina virtual completa desde un punto de recuperación. Los datos no se descifran por la Copia de seguridad de Azure como parte de la operación de restauración.|
| Descifrado | BitLocker o dmcrypt | Para leer datos de un disco de datos restaurada o una máquina virtual restaurada, el software necesita la clave desde el software de administración de claves. Sin la clave correcta, no se pueden descifrar los datos. |

> [AZURE.IMPORTANT]La administración de claves, incluida la sustitución de claves, no forma parte de la Copia de seguridad de Azure. Este aspecto debe administrarse de forma independiente, pero es muy importante para el funcionamiento general de la copia de seguridad o la restauración.

## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) es una solución de cifrado de máquina virtual que se puede usar para proteger los datos de la máquina virtual de IaaS de Azure. CloudLink SecureVM es compatible con el uso de la Copia de seguridad de Azure.

### Información de compatibilidad

- CloudLink SecureVM versión 4.0 (versión 21536.121 o superior)
- Azure PowerShell versión 0.9.8 o posterior

### Administración de claves

Cuando necesite sustituir o cambiar claves para máquinas virtuales que tienen copias de seguridad existentes, debe asegurarse de que las claves usadas en el momento de la copia de seguridad están disponibles. Una sugerencia para hacerlo es realizar una copia de seguridad del almacén de claves o de todo el sistema SecureVM.

### Documentación y recursos

- [Guía de implementación - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [Implementación y uso de SecureVM - vídeo](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_1203_2015-->
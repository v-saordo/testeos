<properties
   pageTitle="Tabla de límites de Copia de seguridad de Azure"
   description="Describe los límites del sistema para Copia de seguridad de Azure."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="10/05/2015" ms.author="trinadhk";"jimpark"; "aashishr" />


Los límites siguientes corresponden a Copia de seguridad de Azure.

| Identificador de límites | Límite predeterminado |
|---|---|
|Número de servidores o máquinas que se pueden registrar en cada almacén|50 para Windows Server/Client/SCDPM <br/> 200 para máquinas virtuales de IaaS|
|Tamaño del origen de datos para los datos almacenados en un almacén de Azure|54 400 GB como máximo<sup>1</sup>|
|Número de almacenes de copia de seguridad que se pueden crear en cada suscripción a Azure|25|
|Número de veces por día que se puede programar la copia de seguridad|3 por día para servidor o cliente Windows <br/> 2 por día para SCDPM <br/> 1 vez al día para máquinas virtuales de IaaS|
|Discos de datos conectados a una máquina virtual de Azure para copia de seguridad|16|

- <sup>1</sup>El límite de 54 400 GB no se aplica a la copia de seguridad de máquinas virtuales IaaS.

<!---HONumber=Oct15_HO3-->
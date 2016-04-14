<properties 
   pageTitle="Hardware para interfaces StorSimple de 10 GbE | Microsoft Azure"
   description="Describe los transceptores acoplables de factor de forma pequeño (SFP), cables y conmutadores admitidos para interfaces de red de 10 GbE en el dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/26/2016"
   ms.author="alkohli" />

# Hardware compatible para interfaces de red de 10 GbE en el dispositivo StorSimple

## Información general

En este artículo se proporciona información sobre el hardware adicional que funciona con el dispositivo Microsoft Azure StorSimple.

## Lista de dispositivos probados por Microsoft

Microsoft ha probado los siguientes transceptores, cables y conmutadores acoplables de factor de forma pequeño (SFP) para garantizar que funcionan correctamente con los dispositivos. (Las siguientes tablas se actualizarán a medida que se pruebe nuevo hardware.)

### Transceptores SFP+

|Asegúrese|Modelo|
|---|---|
|Cisco|SFP-10G-SR|

### Cables

|S. N.º |Asegúrese|Modelo|
|---|---|---|
| 1\.|Cisco|SFP-H10GB-CU1M|
| 2\.|Cisco|SFP-H10GB-CU2M|
| 3\.|Cisco|SFP-H10GB-CU3M|
| 4\.|Tripp-Lite|N820-05M (OM3)|

### Conmutadores

|S. N.º|Asegúrese|Modelo|
|---|---|---|
| 1\. |Cisco|N3K-C3172PQ-10GE|
| 2\. |Cisco|N3K-C3048-ZM-F|
| 3\. |Cisco|N5K-C5596UP-FA|

## Lista de dispositivos probados sobre el terreno

Esta sección contiene la lista de dispositivos que se implementaron correctamente en el terreno por los clientes de StorSimple. Microsoft no los ha probado pero probablemente funcionan con el dispositivo StorSimple.
 
| Parámetro | Valor |
|-----------------------------------|------------------------------------------|
| Proveedor de conmutador | Juniper |
| Modelo de conmutador | ex4550-32F |
| Versión del sistema operativo del conmutador | JunOS 12.3R9.4 |
| Modelo Blade | Puertos incorporados (PIC 0) |
| Proveedor de transceptor | Juniper |
| Modelo de transceptor | Número de pieza 740-021308 <br></br> Número de pieza 740-030658 |
| Versión del firmware del transceptor | Rev 01 versión 0.0 (comunicado) |
| Modelo de cable | Duplex jumper LC/LC 50/125µ, OM3, LSZH |
| Modelo de StorSimple | 8600 |
| Versión del software de StorSimple | 6\.3.9600.17491 |


## Lista de dispositivos probados por el proveedor OEM (Mellanox)  

Mellanox ha probado los siguientes transceptores acoplables de factor de forma pequeño (SFP), cables y conmutadores para garantizar que funcionan correctamente con las interfaces de red Mellanox como las interfaces de red 10 GbE en el dispositivo StorSimple.

### Cables y módulos compatibles con Mellanox 

En la tabla siguiente se enumeran los cables y módulos compatibles con Mellanox. Microsoft no los ha probado pero probablemente funcionan con el dispositivo StorSimple.

| S. N.º | Velocidad | Modelo | Descripción | Asegúrese |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1\. | 10 GbE| CAB-SFP-SFP-1M | cable de cobre pasivo SFP+ 10 Gb/s 1 m | Arista |
| 2\. | 10 GbE| CAB-SFP-SFP-2M | cable de cobre pasivo SFP+ 10 Gb/s 2 m | Arista |
| 3\. | 10 GbE| CAB-SFP-SFP-3M | cable de cobre pasivo SFP+ 10 Gb/s 3 m | Arista |
| 4\. | 10 GbE| CAB-SFP-SFP-5M | cable de cobre pasivo SFP+ 10 Gb/s 5 m | Arista |
| 5\. | 10 GbE| Cisco SFP-H10GBCU1M | Cable Cisco SFP+ | Cisco |
| 6\. | 10 GbE| Cisco SFP-H10GBCU3M | Cable Cisco SFP+ | Cisco |
| 7\. |10 GbE | Cisco SFP-H10GBCU5M | Cable Cisco SFP+ | Cisco |
| 8\. | 10 GbE| J9281B HP X242 10G | Cable de cobre de acoplamiento directo SFP+ a SFP+ 1 m | HP |
| 9\. | 10 GbE| 455883-B21 HP BLc | 10Gb SR SFP+ Opt | HP |
| 10\. | 10 GbE| 455886-B21 HP BLc | 10Gb LR SFP+ Opt | HP |
| 11\. |10 GbE | 487649-B21 HP BLc | Cable de cobre SFP+ 0,5 m 10 GbE | HP |
| 12\. |10 GbE | 487652-B21 HP BLc | Cable de cobre SFP+ 1 m 10 GbE | HP |
| 13\. |10 GbE | 487655-B21 HP BLc | Cable de cobre SFP+ 3 m 10 GbE | HP |
| 14\. |10 GbE | 487658-B21 HP BLc | Cable de cobre SFP+ 7 m 10 GbE | HP |
| 15\. |10 GbE | 537963-B21 HP BLc | Cable de cobre SFP+ 5 m 10 GbE | HP |
| 16\. |10 GbE | AP784A HP | Cable SFP+ de cobre pasivo serie C 3 m | HP |
| 17\. |10 GbE | AP785A HP | Cable SFP+ de cobre pasivo serie C 5 m | HP |
| 18\. |10 GbE | AP818A HP | Cable SFP+ de cobre activo serie B 1 m | HP |
| 19\. |10 GbE | AP819A HP | Cable SFP+ de cobre activo serie B 3 m | HP |
| 20\. |10 GbE | J9150A HP | Transceptor X 132 10G SFP+ LC SR | HP |
| 21\. |10 GbE | J9151A HP | Transceptor X 132 10G SFP+ LC LR | HP |
| 22\. |10 GbE | J9283B HP | Cable DAC X242 10G SFP+ SFP+ de 3 m | HP |
| 23\. |10 GbE | HP J9285B | Cable DAC X242 10G SFP+ SFP+ 7 m | HP |
| 24\. | 10 GbE| JD095B HP | Cable DAC X240 10G SFP+ SFP+ 0,65 m | HP |
| 25\. | 10 GbE| JD096B HP | Cable DAC X240 10G SFP+ SFP+ 1,2 m | HP |
| 26\. | 10 GbE| JD097B HP | Cable DAD X240 10G SFP+ SFP+ 3 m | HP |
| 27\. | 10 GbE| MAM1Q00A-QSA Mellanox | Adaptador de QSFP a SFP+ | Mellanox Technologies |
| 28\. | 10 GbE| MC2309124-006 Mt | Cable de cobre pasivo 1x SFP + a QSFP 10Gb/s 24awg 7 m | Mellanox Technologies |
| 29\. | 10 GbE| MC2309124-007 Mt | Cable de cobre pasivo 1x SFP + a QSFP 10Gb/s 24awg 7 m | Mellanox Technologies |
| 30\. | 10 GbE| MC2309130-003 Mt | Cable de cobre pasivo 1x SFP+ a QSFP 10Gb/s 30awg 3 m | Mellanox Technologies |
| 31\. | 10 GbE| MC2309130-00A Mt | Cable de cobre pasivo 1x SFP+ a QSFP 10Gb/s 30awg 0,5 m | Mellanox Technologies |
| 32\. | 10 GbE| MC3309124-005 Mt | Cable de cobre pasivo 1x SFP+ 10Gb/s 24awg 5 m | Mellanox Technologies |
| 33\. | 10 GbE| MC3309124-007 Mt | Cable de cobre pasivo 1x SFP+ 10Gb/s 24awg 7 m | Mellanox Technologies |
| 34\. | 10 GbE| MC3309130-003 Mt | Cable de cobre pasivo 1x SFP+ 10Gb/s 30awg 3 m | Mellanox Technologies |
| 35\. | 10 GbE| MC3309130-00A Mt | Cable de cobre pasivo 1x SFP+ 10Gb/s 30awg 0,5 m | Mellanox Technologies |

### Lista de conmutadores compatibles con Mellanox 

En la tabla siguiente se enumeran los conmutadores y módulos compatibles con Mellanox. Microsoft no los ha probado pero probablemente funcionan con el dispositivo StorSimple.

| S. N.º | Velocidad | Modelo | Descripción | Asegúrese |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1\. | 10 GbE | 516733-B21 | Conmutador Blade HP ProCurve 6120XG 10GbE Ethernet | HP |
| 2\. | 10 GbE | 538113-B21 | Módulo de paso a través HP 10GbE (PTM) | HP |
| 3\. | 10 GbE | EN4093 | Módulo de conmutador escalable IBM PureFlex System Fabric EN4093 de 10 Gigabits | IBM |
| 4\. | 1 GbE | 3020 | Conmutador Blade Cisco Catalyst 3020 1 GbE | Cisco |
| 5\. | 1 GbE | 3020X | Conmutador Blade Cisco Catalyst 3020X 1GbE | Cisco |
| 6\. | 1 GbE | 438030-B21 | Módulo de conmutador HP 1 GbE - conmutador Blade GbE2c Layer 2/3 Ethernet | HP |
| 7\. | 1 GbE | 6120G | Conmutador Blade HP ProCurve 6120G/XG 1 GbE | HP |

## Pasos siguientes

[Obtenga más información sobre los componentes de hardware de StorSimple y su estado](storsimple-monitor-hardware-status.md).

<!---HONumber=AcomDC_0128_2016-->
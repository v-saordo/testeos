<properties
   pageTitle="Usar Copia de seguridad de Azure para reemplazar la infraestructura de cintas | Microsoft Azure"
   description="Aprenda cómo la Copia de seguridad de Microsoft Azure proporciona semántica similar a la cinta que le permite hacer copias de seguridad y restaurar datos en Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags  ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="12/15/2015" ms.author="jimpark"; "aashishr"; "sammehta"/>

# Usar la copia de seguridad de Azure para cambiar su infraestructura de cintas
Los clientes de Copia de seguridad de Azure y System Center Data Protection Manager pueden: - realizar una copia de seguridad de los datos en las programaciones que se adaptan mejor a las necesidades de su organización; - retener los datos de copia de seguridad durante períodos más largos; - implicar a Azure como parte de sus necesidades de retención a largo plazo (en lugar de usar cintas).

Este artículo explica cómo los clientes pueden habilitar las directivas de copia de seguridad y retención. Los clientes que utilicen las cintas para abordar sus necesidades de retención a largo plazo ahora tienen una alternativa viable y eficaz con la disponibilidad de esta característica. La característica está habilitada en la versión más reciente de la copia de seguridad de Azure (que está disponible [aquí](http://aka.ms/azurebackup_agent)). Los clientes de SCDPM deberán cambiar a UR5 antes de usar esta característica.

## ¿Cuál es la programación de copia de seguridad?
La programación de copia de seguridad indica la frecuencia de la operación de copia de seguridad. Por ejemplo, la configuración de la pantalla siguiente indica que las copias de seguridad se tomarán diariamente a las 18:00 h y a medianoche.

![Programación diaria](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Los clientes también pueden programar una copia de seguridad semanal. Por ejemplo, la configuración de la pantalla siguiente indica que las copias de seguridad se realizarán alternando entre cada domingo y miércoles a las 09:30 h y a la 01: 00 h.

![Programación semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## ¿Qué es la directa de retención?
La directiva de retención especifica el tiempo durante el que debe almacenarse la copia de seguridad. En vez de especificar solo una "directiva" para todos los puntos de copia de seguridad, los clientes pueden especificar directivas de retención diferentes en función de cuándo se realizó la copia de seguridad. Por ejemplo, puede que el punto de copia de seguridad que se toma al final de cada trimestre se tenga que conservar durante más tiempo con fines de auditoría, mientras que el punto de copia de seguridad diario (que sirve como punto de recuperación operativo) debe mantenerse durante 90 días.

![Directiva de retención](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

El número total de "puntos de retención" especificado en esta directiva es de 90 (puntos diarios) + 40 (uno cada trimestre durante 10 años) = 130.

## Ejemplo: reunir ambos

![Pantalla de ejemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Directiva de retención diaria**: las copias de seguridad realizadas diariamente se almacenan durante 7 días.
2. **Directiva de retención semanal**: las copias de seguridad realizadas cada sábado a medianoche y a las 18:00 h se conservarán durante 4 semanas.
3. **Directiva de retención mensual**: las copias de seguridad realizadas a medianoche y a las 6 p.m. del último sábado de cada mes se conservarán durante 12 meses.
4. **Directiva de retención anual**: las copias de seguridad realizadas del último sábado de cada mes de marzo se conservarán durante 10 días.

El número total de "puntos de retención" (desde los que un cliente puede restaurar datos) en el diagrama anterior se calcula de la forma siguiente:

- 2 puntos por día durante 7 días = 14 puntos de recuperación
- 2 puntos por semana durante 4 semanas = 8 puntos de recuperación
- 2 puntos por mes durante 12 meses = 24 puntos de recuperación
- 1 punto por año durante 10 años = 10 puntos de recuperación

El número total de puntos de recuperación es 56.

> [AZURE.NOTE] La copia de seguridad de Azure no tiene una restricción sobre el número de puntos de recuperación.

## Configuración avanzada
Al hacer clic en **Modificar** en la pantalla anterior, los clientes tienen más flexibilidad para especificar programaciones de retención.

![Modify](./media/backup-azure-backup-cloud-as-tape/modify.png)

## Pasos siguientes
Para obtener más información sobre Copia de seguridad de Azure, vea

- [Introducción a la Copia de seguridad de Azure](backup-introduction-to-azure-backup.md)
- [Probar Copia de seguridad de Azure](backup-try-azure-backup-in-10-mins)

<!---HONumber=AcomDC_0128_2016-->
---
title: Автоматизированная установка исправлений для виртуальных машин SQL Server (классическая модель) | Документация Майкрософт
description: Описывается функция автоматической установки исправлений для виртуальных машин SQL Server в Azure, использующих классический режим развертывания.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978083"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (классическая модель)
> [!div class="op_single_selector"]
> * [Менеджер ресурсов](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Классический](../classic/sql-automated-patching.md)
> 
> 

При автоматической установке исправлений на виртуальных машинах Azure с SQL Server задается период обслуживания. Установка автоматических обновлений возможна только в этот период обслуживания. Для SQL Server это гарантирует, что системные обновления и связанные с ними перезапуски системы будут происходить в наиболее удобное для базы данных время. 

> [!IMPORTANT]
> Устанавливаются только те обновления ОС Windows, которые помечены как **Важные**. Другие обновления SQL Server (например, накопительные обновления) нужно устанавливать вручную. 

Автоматическая установка исправлений зависит от [Расширения агента IaaS для SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure имеет две различные модели развертывания для создания и работы с ресурсами: [менеджер ресурсов и Classic.](../../../azure-resource-manager/management/deployment-models.md) В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.  Версию этой статьи для Resource Manager см. в разделе [Автоматическое исправление SQL Server на виртуальных машинах Azure Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Предварительные требования
Для использования автоматической установки исправлений необходимо выполнить следующие предварительные требования.

**Операционная система**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Версия сервера S'L**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Установите последнюю версию команд Azure PowerShell](/powershell/azure/overview).

**Расширение IaaS для SQL Server**

* [Установите расширение IaaS для SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Параметры
В приведенной ниже таблице описаны параметры для настройки автоматической установки исправлений. Для виртуальных машин, развернутых с использованием классической модели, эти параметры необходимо настроить с помощью PowerShell.

| Параметр | Возможные значения | Описание |
| --- | --- | --- |
| **Автоматическое исправление** |Включено/отключено (отключено) |Включает или отключает автоматическую установку исправлений для виртуальной машины Azure. |
| **Расписание обслуживания** |Каждый день, понедельник, вторник, среда, четверг, пятница, суббота, воскресенье |Расписание для скачивания и установки обновлений Windows, SQL Server и обновлений Майкрософт для виртуальной машины. |
| **Время начала обслуживания** |0–24 |Локальное время начала обновления виртуальной машины. |
| **Длительность периода обслуживания** |30–180 |Допустимое количество минут для скачивания и установки обновлений. |
| **Категория исправления** |Важно! |Категория обновлений, которые будут скачаны и установлены. |

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell
В следующем примере для настройки автоматической установки исправлений на существующей виртуальной машине SQL Server используется PowerShell. Команда **New-AzureVMSqlServerAutoPatchingConfig** настраивает новый период обслуживания для автоматических исправлений.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

В представленной ниже таблице показано фактическое воздействие на конечную виртуальную машину Azure на основе данного примера.

| Параметр | Действие |
| --- | --- |
| **Dayofweek** |Исправления устанавливаются каждый четверг. |
| **MaintenanceWindowStartingHour** |Установка обновлений начинается в 11:00. |
| **MaintenanceWindowDuration** |Обновления должны быть установлены в течение 120 минут. С учетом времени начала установка обновлений должна завершаться к 13:00. |
| **PatchCategory** |Единственное возможное значение для этого параметра — Important (Важно). |

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы отключить автоматическую установку обновлений, выполните тот же скрипт без параметра -Enable в команде New-AzureVMSqlServerAutoPatchingConfig. Как и установка, отключение автоматической установки исправлений занимает несколько минут.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](../classic/sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).


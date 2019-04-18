---
title: Автоматизированная установка исправлений для виртуальных машин SQL Server (Resource Manager) | Документация Майкрософт
description: Описывает функцию автоматической установки исправлений для виртуальных машин SQL Server в Azure для модели Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 348979a53bff76c85e6d1531bd16cd695145e21b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425991"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

При автоматической установке исправлений на виртуальных машинах Azure с SQL Server задается период обслуживания. Установка автоматических обновлений возможна только в этот период обслуживания. Для SQL Server это ограничение гарантирует, что системные обновления и связанные с ними перезапуски системы будут происходить в наиболее удобное для базы данных время. 

> [!IMPORTANT]
> Устанавливаются только те обновления ОС Windows, которые помечены как **Важные**. Другие обновления SQL Server (например, накопительные обновления) нужно устанавливать вручную. 

Автоматическая установка исправлений зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Технические условия
Для использования автоматической установки исправлений необходимо выполнить следующие предварительные требования.

**Операционная система**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Версия SQL Server**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**

* [Установите последние команды Azure PowerShell](/powershell/azure/overview) , если планируете настраивать автоматические исправления с помощью PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Автоматическая установка исправлений зависит от расширения агента IaaS для SQL Server. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Расширение агента SQL Server IaaS).
> 
> 

## <a name="settings"></a>Параметры
В приведенной ниже таблице описаны параметры для настройки автоматической установки исправлений. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

| Параметр | Возможные значения | ОПИСАНИЕ |
| --- | --- | --- |
| **Автоматическое исправление** |Включено/отключено (отключено) |Включает или отключает автоматическую установку исправлений для виртуальной машины Azure. |
| **Расписание обслуживания** |Каждый день, понедельник, вторник, среда, четверг, пятница, суббота, воскресенье |Расписание для скачивания и установки обновлений Windows, SQL Server и обновлений Майкрософт для виртуальной машины. |
| **Время начала обслуживания** |0–24 |Локальное время начала обновления виртуальной машины. |
| **Длительность периода обслуживания** |30–180 |Допустимое количество минут для скачивания и установки обновлений. |
| **Категория исправления** |Важно! | Категория обновлений ОС Windows, которые будут скачаны и установлены.|

## <a name="configuration-in-the-portal"></a>Настройка на портале
Для настройки автоматизированной установки исправлений во время подготовки виртуальных машин или для существующих виртуальных машин можно использовать портал Azure.

### <a name="new-vms"></a>Новые виртуальные машины
При создании новой виртуальной машины SQL Server с моделью развертывания с помощью Resource Manager настройте автоматизированную установку исправлений, используя портал Azure.

В колонке **Параметры SQL Server** выберите **Автоматизированное исправление**. Колонка **Автоматическая установка исправлений SQL** показана на следующем снимке экрана портала Azure.

![Автоматизированная установка исправлений SQL на портале Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Для контекста обратитесь к полному описанию в разделе [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная установка исправлений SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированное исправление".

![Настройка автоматизированной установки исправлений SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная установка исправлений включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. В течение этого времени портал Azure может не отображать информацию о том, что выполняется настройка автоматической установки исправлений. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell
После подготовки виртуальной машины SQL используйте PowerShell для настройки автоматической установки исправлений.

В следующем примере для настройки автоматической установки исправлений на существующей виртуальной машине SQL Server используется PowerShell. **New AzVMSqlServerAutoPatchingConfig** команда настраивает новый период обслуживания для автоматических обновлений.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Если расширение не установлено, при его установке перезапускается служба SQL Server.

В представленной ниже таблице показано фактическое воздействие на конечную виртуальную машину Azure на основе данного примера.

| Параметр | Эффект |
| --- | --- |
| **DayOfWeek** |Исправления устанавливаются каждый четверг. |
| **MaintenanceWindowStartingHour** |Установка обновлений начинается в 11:00. |
| **MaintenanceWindowsDuration** |Обновления должны быть установлены в течение 120 минут. С учетом времени начала установка обновлений должна завершаться к 13:00. |
| **PatchCategory** |Единственное возможное значение для этого параметра — **Important**. При этом устанавливаются обновления ОС Windows с пометкой "Важно". Обновления SQL Server, не помеченные как важные, не устанавливаются. |

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы отключить автоматизированную установку исправлений, выполните тот же сценарий без **-включить** параметр **New AzVMSqlServerAutoPatchingConfig**. Отсутствие параметра **-Enable** означает, что функцию нужно отключить.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).


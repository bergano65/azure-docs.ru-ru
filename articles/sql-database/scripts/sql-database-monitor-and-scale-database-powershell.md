---
title: Пример для CLI. Мониторинг и масштабирование отдельной базы данных SQL Azure | Документация Майкрософт
description: Пример сценария Azure PowerShell для отслеживания и масштабирования отдельной базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 0972df18c65ee6b61db4113eaebc1f2912cd6064
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892886"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>Мониторинг и масштабирование отдельной базы данных SQL с помощью PowerShell

Этот пример сценария PowerShell отслеживает метрики производительности базы данных, увеличивает ее вычислительную мощность и создает правило генерации оповещений для одной из метрик производительности.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, вам понадобится AZ PowerShell 1.4.0 или последующей версии для работы с этим руководством. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!TIP]
> Сведения о состоянии операций с базой данных можно получить при помощи [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity). Чтобы отменить операцию обновления в базе данных, выполните [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещена отдельная база данных или пул эластичных баз данных. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Отображает сведения об используемом размере базы данных.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Обновляет свойства базы данных или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Задает правило генерации оповещений для автоматического отслеживания единиц DTU в будущем. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).

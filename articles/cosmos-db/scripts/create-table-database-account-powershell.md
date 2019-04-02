---
title: Скрипт Azure PowerShell для создания учетной записи API таблиц для Azure Cosmos DB
description: Пример скрипта Azure PowerShell для создания учетной записи API таблицы для Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: PowerShell
ms.topic: sample
ms.date: 08/08/2018
ms.reviewer: sngun
ms.openlocfilehash: 4f4ad89f32c8ddcb2c3d9fb7f21bf274cc3586ef
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496451"
---
# <a name="azure-cosmos-db-create-a-table-api-account-using-powershell"></a>Azure Cosmos DB — Создание учетной записи API таблиц с помощью PowerShell

В этом примере скрипта PowerShell создается учетная запись API таблицы для Azure Cosmos DB. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/cosmosdb/create-and-configure-Table-database/create-and-configure-Table-database.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Создает логический сервер, на котором размещена база данных или эластичный пул. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../powershell-samples.md).

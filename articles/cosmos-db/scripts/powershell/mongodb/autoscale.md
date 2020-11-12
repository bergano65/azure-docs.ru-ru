---
title: Скрипт PowerShell для создания базы данных и коллекции с автомасштабированием для API MongoDB в Azure Cosmos
description: Скрипт Azure PowerShell для создания базы данных и коллекции с автомасштабированием для API MongoDB в Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: bd455af8b8a9a8a293165616e490d4b4d1c79f7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075490"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>Создание базы данных и коллекции с автомасштабированием в Azure Cosmos DB — API MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Создает учетную запись Cosmos DB. |
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Создает базу данных API MongoDB. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Создает индекс API MongoDB. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Создает коллекцию API MongoDB. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/).

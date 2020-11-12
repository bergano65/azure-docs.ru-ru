---
title: Скрипт PowerShell для создания контейнера с отключенным индексированием в учетной записи Azure Cosmos DB
description: Пример скрипта Azure PowerShell — создание контейнера с отключенным индексированием в учетной записи Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: bf3aa4844d990becc27ede222f2649ccbd3ab91b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089616"
---
# <a name="create-a-container-with-indexing-turned-off-in-an-azure-cosmos-db-account-using-powershell"></a>Создание контейнера с отключенным индексированием в учетной записи Azure Cosmos DB с помощью PowerShell
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container with indexing turned off in an Azure Cosmos DB account")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Создание учетной записи Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Создание базы данных SQL Cosmos DB. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Создание объекта PSSqlIndexingPolicy, который используется в качестве параметра для командлета New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Создание контейнера SQL в Cosmos DB. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/).
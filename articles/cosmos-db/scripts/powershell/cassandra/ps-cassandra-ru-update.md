---
title: Скрипт PowerShell для обновления ЕЗ/с в ресурсах API Cassandra службы Azure Cosmos DB
description: Узнайте, как с помощью скрипта PowerShell обновить показатель пропускной способности для пространства ключей или таблицы в API Cassandra Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: ef10dd7566d74a112a849f500da1831bc8ccb682
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365687"
---
# <a name="update-rus-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Обновление показателя ЕЗ/с для пространства ключей или таблицы в Azure Cosmos DB — API Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

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
| [Set-AzCosmosDBCassandraKeyspace](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandrakeyspace) | Создает или обновляет пространство ключей API Cassandra в службе Cosmos DB. |
| [New-AzCosmosDBCassandraClusterKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Создает новый ключ кластера Cassandra в службе CosmosDB. |
| [New-AzCosmosDBCassandraColumn](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Создает новый столбец Cassandra в службе CosmosDB. |
| [New-AzCosmosDBCassandraSchema](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Создает новую схему Cassandra в службе CosmosDB. |
| [Set-AzCosmosDBCassandraTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandratable) | Создает или обновляет таблицу API Cassandra в службе Cosmos DB. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../../../powershell-samples.md).
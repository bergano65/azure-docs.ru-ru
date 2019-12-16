---
title: Скрипт Azure PowerShell — обновление ЕЗ/с в Azure Cosmos DB для API MongoDB
description: Узнайте, как с помощью скрипта PowerShell обновить показатель пропускной способности для базы данных или контейнера в API для MongoDB Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 626904c4e281c7ce3ce632412daa24ecdb56938e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871913"
---
# <a name="update-rus-for-a-database-or-collection-for-azure-cosmos-db---mongodb-api"></a>Обновление ЕЗ/с для базы данных или коллекции в Azure Cosmos DB — API MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-update.ps1 "Update throughput on a database or collection for MongoDB API")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
|**Ресурсы Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Создает ресурс. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../../../powershell-samples.md).
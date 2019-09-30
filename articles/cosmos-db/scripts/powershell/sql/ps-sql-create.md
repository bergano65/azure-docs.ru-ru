---
title: Скрипт Azure PowerShell — создание базы данных и контейнера для Azure Cosmos DB с помощью API SQL (Core)
description: Скрипт Azure PowerShell — создание базы данных и контейнера для Azure Cosmos DB с помощью API SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: eee1e31808412dc5e4308dee92f3685507e771f3
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178806"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Создание базы данных и контейнера в Azure Cosmos DB — API SQL (Core)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает учетную запись Cosmos для API SQL (Core) в двух регионах с согласованностью на уровне сеанса, базой данных с общей пропускной способностью и контейнером с ключом секции, пользовательской политикой индексирования, политикой уникального ключа, сроком жизни, выделенной пропускной способностью и политикой разрешения конфликтов, реализующей подход "Сохраняются изменения, внесенные последними", с задаваемым пользователем путем разрешения конфликтов, который будет использоваться при `multipleWriteLocations=true`.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

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

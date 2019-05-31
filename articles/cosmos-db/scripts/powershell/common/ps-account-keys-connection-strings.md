---
title: Сценарий Azure PowerShell — ключ учетной записи и соединения строковые операции для учетной записи Azure Cosmos
description: Пример скрипта Azure PowerShell — учетная запись ключа и подключения строковые операции для учетной записи Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a7f86bfee0ee2472260b622dc709dc437e53414c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247864"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-account-using-powershell"></a>Строка подключения и учетной записи ключа операции для учетной записи Azure Cosmos с помощью PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

В этом требует существовать группа ресурсов и учетной записи. Используйте существующие PowerShell Создание примера сначала подготовить учетную запись.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

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
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Вызывает действие для ресурса. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../../../powershell-samples.md).
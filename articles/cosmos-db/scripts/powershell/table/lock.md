---
title: Скрипт PowerShell для создания блокировки ресурсов для таблицы API таблиц в Azure Cosmos
description: Создание блокировки ресурсов для таблицы API таблиц в Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 4d5bb50d4fa34176da63c1305462903e9b62f010
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504732"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Создание блокировки ресурса для таблицы API таблиц в Azure Cosmos с помощью Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Блокировки ресурсов не работают для изменений, внесенных пользователями, которые подключаются с помощью пакета Cosmos DB SDK и любых средств, которые используют ключи учетной записи, если только учетная запись Cosmos DB сначала не будет заблокирована с включенным свойством `disableKeyBasedMetadataWriteAccess`. Дополнительные сведения о том, как включить это свойство, см. в разделе [Предотвращение изменений в пакетах SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Пример скрипта

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
|**Ресурс Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/new-azresourcelock) | Создает блокировку ресурса. |
| [Get-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/get-azresourcelock) | Возвращает блокировку ресурса или список блокировок ресурсов. |
| [Remove-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/remove-azresourcelock) | Удаляет блокировку ресурса. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/PowerShell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../../../PowerShell-samples.md).
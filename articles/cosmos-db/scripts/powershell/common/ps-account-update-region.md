---
title: Скрипт PowerShell для обновления регионов учетной записи Azure Cosmos
description: Пример скрипта Azure PowerShell для обновления регионов учетной записи Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: 4a8e24b4445e2bf29b35e87600de260c523d4376
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367177"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Обновление регионов учетной записи Azure Cosmos с помощью PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Пример скрипта

> [!NOTE]
> Нельзя изменять регионы вместе с другими свойствами учетной записи Cosmos в рамках одной операции. Такие изменения должны выполняться поочередно.
> [!NOTE]
> В этом примере демонстрируется использование учетной записи API SQL (Core). Чтобы использовать этот пример с другими API, соответствующие свойства необходимо скопировать в скрипт для такого API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
|**Ресурсы Azure**| |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Обновление ресурса. |
|**Группы ресурсов Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры скриптов PowerShell для базы данных Azure Cosmos DB можно найти [здесь](../../../powershell-samples.md).

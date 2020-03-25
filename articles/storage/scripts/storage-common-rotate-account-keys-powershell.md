---
title: Смена ключей учетной записи хранения с помощью PowerShell
titleSuffix: Azure Storage
description: Сведения о создании учетной записи хранения Azure и последующей операции извлечения и смены одного из ее ключей доступа.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 52ebed3de093f15d8188ee5fec49d75d5a4a206d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060813"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Смена ключей учетной записи хранения с помощью PowerShell

Приведенный в этой статье скрипт создает учетную запись хранения Azure, отображает первичный ключ доступа новой учетной записи хранения, а затем обновляет (меняет) его.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните команду ниже, чтобы удалить группу ресурсов, учетную запись хранения и все связанные ресурсы.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Описание скрипта

Ниже приведены команды, на основе которых скрипт создает учетную запись хранения, извлекает и обновляет один из ее ключей доступа. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Выводит все расположения и поддерживаемых в каждом из них поставщиков ресурсов. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группы ресурсов Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Создает учетную запись хранения. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Выводит список ключей доступа для учетной записи хранения Azure. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Повторно создает ключ доступа учетной записи хранения Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

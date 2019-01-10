---
title: Пример скрипта Azure PowerShell. Смена ключа доступа учетной записи хранения | Документация Майкрософт
description: Сведения о создании учетной записи хранения Azure и последующей операции извлечения и смены одного из ее ключей доступа.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 026c399af70a0c97446fba28b5dd7ca1ed82b89c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635499"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Создание учетной записи хранения и смена ее ключей доступа

Приведенный в этой статье скрипт создает учетную запись хранения Azure, отображает первичный ключ доступа новой учетной записи хранения, а затем обновляет (меняет) его.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

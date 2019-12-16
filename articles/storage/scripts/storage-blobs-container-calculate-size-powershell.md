---
title: Вычисление размера контейнера больших двоичных объектов с помощью PowerShell
titleSuffix: Azure Storage
description: Сведения о вычислении размера контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 890da96ac1d97a2eb84f5296c74a02824d657273
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895046"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Вычисление размера контейнера больших двоичных объектов с помощью PowerShell

Этот скрипт позволяет вычислить размер контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Этот скрипт PowerShell предоставляет прогнозируемый размер контейнера и не должен использоваться для вычислений при выставлении счетов. Дополнительные сведения о скрипте, который позволяет вычислить размер контейнера для выставления счетов, см. в разделе [Вычисление общего указываемого в счете размера контейнера больших двоичных объектов](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните команду ниже, чтобы удалить группу ресурсов, контейнер и все связанные ресурсы.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Описание скрипта

Ниже приведены команды, на основе которых скрипт вычисляет размер контейнера в хранилище BLOB-объектов. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Возвращает определенную учетную запись хранения или все учетные записи хранения в группе ресурсов или подписке. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Возвращает список больших двоичных объектов в контейнере. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о скрипте, который позволяет вычислить размер контейнера для выставления счетов, см. в разделе [Вычисление общего указываемого в счете размера контейнера больших двоичных объектов](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

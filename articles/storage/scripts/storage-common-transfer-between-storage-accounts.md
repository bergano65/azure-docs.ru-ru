---
title: Перенос больших двоичных объектов между различными учетными записями хранения с помощью AzCopy в ОС Windows
description: Пример скрипта Azure PowerShell. Копирование содержимого большого двоичного объекта между учетными записями хранения Azure с помощью AzCopy.
services: storage
documentationcenter: na
author: normesta
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: normesta
ms.openlocfilehash: 559b8b2875b789034ae07901f668f241505073b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465060"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Перенос больших двоичных объектов между различными учетными записями хранения с помощью AzCopy в ОС Windows

В этом примере показано, как скопировать все большие двоичные объекты из исходной учетной записи хранения, предоставленной пользователем, в целевую. 

Для этого используется команда `Get-AzStorageContainer`, которая перечисляет все контейнеры в учетной записи хранения. Затем, как показано в примере, AzCopy выдает команды для копирования каждого контейнера из исходной учетной записи хранения источника в целевую. В случае сбоя выполняется повторная попытка столько раз, сколько указано в свойстве $retryTimes (по умолчанию — 3 раза, что можно изменить в параметре `-RetryTimes`). Если при каждой повторной попытке возникают ошибки, то пользователь может перезапустить сценарий, указав в примере последний успешно скопированный контейнер с помощью параметра `-LastSuccessContainerName`. Затем с этого места будет продолжена операция копирования.

Для работы с этим примером требуется модуль хранилища Azure PowerShell **0.7** или более поздней версии. С помощью команды `Get-Module -ListAvailable Az.storage` можно проверить, какая версия установлена. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Для этого примера также необходима последняя версия [AzCopy для ОС Windows](https://aka.ms/downloadazcopy). Каталог установки по умолчанию — `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

В этом примере используются имя и ключ исходной и целевой учетной записи хранения, а также полный путь к файлу AzCopy.exe (если он не установлен в каталоге по умолчанию).

Ниже приведены примеры входных данных:

Если AzCopy установлен в каталог по умолчанию:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Если AzCopy не установлен в каталоге по умолчанию:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Если произошел сбой и пример необходимо снова запустить из определенного контейнера: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии для копирования данных между учетными записями хранения используются следующие команды: Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Возвращает контейнеры, связанные с этой учетной записью хранения. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Создает контекст службы хранилища Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

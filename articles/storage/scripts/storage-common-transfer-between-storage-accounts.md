---
title: Пример сценария Azure PowerShell по миграции больших двоичных объектов между учетными записями хранения с помощью AzCopy в ОС Windows | Документация Майкрософт
description: Копирование содержимого большого двоичного объекта между учетными записями хранения Azure с помощью AzCopy.
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
ms.openlocfilehash: 0af945177289760ca0d2a97133a300d9978ce702
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260627"
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

| Команда | Примечания |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Возвращает контейнеры, связанные с этой учетной записью хранения. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Создает контекст службы хранилища Azure. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

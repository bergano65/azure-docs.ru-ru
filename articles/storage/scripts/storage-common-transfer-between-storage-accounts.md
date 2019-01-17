---
title: Пример сценария Azure PowerShell по миграции больших двоичных объектов между учетными записями хранения с помощью AzCopy в ОС Windows | Документация Майкрософт
description: Копирование содержимого большого двоичного объекта между учетными записями хранения Azure с помощью AzCopy.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: eddf85df3f9efb6a742e226e9582e2e284cbd4cf
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215139"
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
```PowerShell
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

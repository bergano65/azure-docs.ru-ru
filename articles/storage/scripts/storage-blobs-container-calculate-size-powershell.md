---
title: Пример скрипта Azure PowerShell Вычисление размера контейнера большого двоичного объекта | Документация Майкрософт
description: Сведения о вычислении размера контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: ec8254478786825d0333e8be24b0dfbea8c18e60
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245156"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Вычисление размера контейнера в хранилище BLOB-объектов

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
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Возвращает список больших двоичных объектов в контейнере. ||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о скрипте, который позволяет вычислить размер контейнера для выставления счетов, см. в разделе [Вычисление общего указываемого в счете размера контейнера больших двоичных объектов](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell хранилища см. в статье [Примеры скриптов Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).

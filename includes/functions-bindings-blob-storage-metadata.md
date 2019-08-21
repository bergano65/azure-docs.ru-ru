---
title: включение файла
description: включение файла
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642140"
---
Триггер BLOB-объектов предоставляет несколько свойств метаданных. Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и тип [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet).

|Свойство  |type  |Описание  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Путь к большому двоичному объекту, активирующему триггер.|
|`Uri`|`System.Uri`|Код URI BLOB-объекта для основного расположения.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Системные свойства BLOB-объекта. |
|`Metadata` |`IDictionary<string,string>`|Определяемые пользователем метаданные для BLOB-объекта.|

Например, следующие примеры скрипта C# и JavaScript записывают путь в большой двоичный объект, активирующий триггер, вместе с контейнером:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

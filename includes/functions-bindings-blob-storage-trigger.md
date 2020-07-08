---
title: Включить имя файла
description: включить файл
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202116"
---
Для активируемого BLOB-объекта можно использовать следующие типы параметров:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* объект POCO, сериализуемый как JSON;
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

Если при попытке привязаться к одному из типов SDK для службы хранилища получено сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Привязку к `string`, `Byte[]` или POCO рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) далее в этой статье.
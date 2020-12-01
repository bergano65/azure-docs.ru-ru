---
title: включить файл
description: включить файл
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 09e56395122f3be897d8bfb34b1d51cee034b505
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356233"
---
Для записи больших двоичных объектов можно выполнить привязку к следующим типам:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>открыт</sup>
* `CloudBlockBlob`<sup>открыт</sup>
* `CloudPageBlob`<sup>открыт</sup>
* `CloudAppendBlob`<sup>открыт</sup>

<sup>1</sup> Требует привязки in `direction` в файле *function.json* или `FileAccess.Read` в библиотеке классов C#. Однако вы можете использовать объект контейнера, предоставляемый средой выполнения для записи таких операций, как отправка больших двоичных объектов в контейнер.

<sup>2</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

Если при попытке привязаться к одному из типов SDK для службы хранилища получено сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Привязку к `string` или `Byte[]` рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) выше в этой статье.

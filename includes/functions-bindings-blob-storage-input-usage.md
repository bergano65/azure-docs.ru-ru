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
ms.openlocfilehash: 060a84c06e65441381a0cf74f20fb4a9d7b0f042
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642153"
---
Для входной привязки BLOB-объектов можно использовать следующие типы параметров:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

Если при попытке привязаться к одному из типов SDK для службы хранилища получено сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

Привязку к `string` или `Byte[]` рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](#trigger---concurrency-and-memory-usage) выше в этой статье.

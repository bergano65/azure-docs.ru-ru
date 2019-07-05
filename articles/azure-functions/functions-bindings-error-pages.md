---
title: Рекомендации по обработке ошибок службы "Функции Azure" | Документация Майкрософт
description: Содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций, а также ссылки на разделы об ошибках, связанных с привязкой.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480710"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Этот раздел содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций. Он также содержит ссылки на разделы, в которых описываются ошибки привязки, которые могут возникнуть. 

## <a name="handing-errors-in-functions"></a>Обработка ошибок в функциях
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции со службами Azure могут возникать ошибки, поступающие из интерфейсов API базовых служб. Ссылки на документацию по кодам ошибок для этих служб можно найти в подразделах **Исключения и коды возврата** следующих справочных разделов, посвященных триггерам и привязкам.

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Центры событий](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)

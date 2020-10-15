---
title: Руководство по обработке ошибок в функциях Azure
description: Узнайте, как управлять ошибками в функциях Azure со ссылками на определенные ошибки привязки.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0617d55f7c67c788b1e898d963f7d509cef72d49
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096850"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Обработка ошибок в функциях Azure важна, чтобы избежать потери данных, пропущенных событий и мониторинга работоспособности приложения.

В этой статье описываются общие стратегии обработки ошибок и ссылки на ошибки привязки.

## <a name="handling-errors"></a>Обработка ошибок

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции со службами Azure ошибки могут исходить из API-интерфейсов базовых служб. Сведения об ошибках, связанных с привязками, доступны в разделе **исключения и коды возврата** в следующих статьях:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Центры событий](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Центры Интернета вещей](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table-output.md#exceptions-and-return-codes)

---
title: Руководство по обработке ошибок в функциях Azure
description: Узнайте, как управлять ошибками в функциях Azure со ссылками на определенные ошибки привязки.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77586195"
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

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)

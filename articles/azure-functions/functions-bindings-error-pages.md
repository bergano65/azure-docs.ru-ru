---
title: Руководство по обработке ошибок в функциях Azure
description: Узнайте, как управлять ошибками в функциях Azure со ссылками на определенные ошибки привязки.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 920b887382ca55d84c66fabb8f1464fd05041094
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198465"
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

+ [Центры событий](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)

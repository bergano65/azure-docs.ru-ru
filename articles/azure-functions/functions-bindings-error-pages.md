---
title: Руководство по обработке ошибок в функциях Azure
description: Узнайте, как управлять ошибками в функциях Azure со ссылками на определенные ошибки привязки.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155286"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Обработка ошибок в функциях Azure важна, чтобы избежать потери данных, пропущенных событий и мониторинга работоспособности приложения.

В этой статье описываются общие стратегии обработки ошибок и ссылки на ошибки привязки.

## <a name="handling-errors"></a>Обработка ошибок

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции со службами Azure ошибки могут исходить из API-интерфейсов базовых служб. Сведения об ошибках, связанных с привязками, доступны в разделе **исключения и коды возврата** в следующих статьях:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Центры событий](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)

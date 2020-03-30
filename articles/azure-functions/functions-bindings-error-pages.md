---
title: Руководство по обработке ошибок Azure Functions
description: Научитесь обрабатывать ошибки в функциях Azure со ссылками на определенные ошибки связывания.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586195"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Обработка ошибок в функциях Azure важна для предотвращения потери данных, пропущенных событий и мониторинга работоспособности приложения.

В этой статье описываются общие стратегии обработки ошибок наряду со ссылками на ошибки, связанные с обязательными.

## <a name="handling-errors"></a>Обработка ошибок

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции с службами Azure ошибки могут возникать из AAP базовых служб. Информация, касающаяся ошибок, связанных с обязательными, доступна в разделе **исключений и кодов возврата** следующих статей:

+ [Azure Космос DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Центры событий](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Концентраторы IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранение очередей](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Хранение таблицы](functions-bindings-storage-table.md#exceptions-and-return-codes)

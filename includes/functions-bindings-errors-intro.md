---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474110"
---
Ошибки, поднятые в функциях Azure, могут исходить от любого из следующих истоков:

- Использование встроенных [триггеров и привязок](..\articles\azure-functions\functions-triggers-bindings.md) Azure
- Звонки в AIS базовых служб Azure
- Звонки в конечные точки REST
- Звонки в клиентские библиотеки, пакеты или сторонние AI

После твердых методов обработки ошибок важно, чтобы избежать потери данных или пропущенных сообщений. Рекомендуемые методы обработки ошибок включают следующие действия:

- [Включение Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Используйте структурированную обработку ошибок](#use-structured-error-handling)
- [Дизайн для идемпотенции](../articles/azure-functions/functions-idempotent.md)
- [Реализация политик повторной притязаний](../articles/azure-functions/functions-reliable-event-processing.md) (при необходимости)

### <a name="use-structured-error-handling"></a>Используйте структурированную обработку ошибок

Захват и публикация ошибок имеет решающее значение для мониторинга работоспособности приложения. Верхний уровень любого функционального кода должен включать блок try/catch. В блоке catch можно зафиксировать и публиковать ошибки.

### <a name="retry-support"></a>Поддержка повтора

Следующие триггеры поддерживают встроенный механизм повторных попыток:

* [Хранение Azure Blob](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Хранение очереди Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [служебная шина Azure (очередь/тема)](../articles/azure-functions/functions-bindings-service-bus.md).

По умолчанию эти триггеры повторного запроса до пяти раз. После пятой повторной попытки и хранилище очереди Azure, и триггеры службы Azure записывают сообщение [в очередь с ядом.](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)

Необходимо вручную реализовать политики повторной попытки для любых других типов триггеров или привязок. Ручные реализации могут включать запись информации об ошибке в [очередь ядовых сообщений.](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs) Написав в очередь яда, у вас есть возможность повторить операции на более позднее время. Такой же подход использует и триггер хранилища BLOB-объектов.

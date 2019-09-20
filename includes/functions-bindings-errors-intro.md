---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f771b6b0416c5777c1ebde7e2cf2c4ffc6f375ff
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155301"
---
Ошибки, возникающие в функциях Azure, могут поступать из любого из следующих источников:

- Использование встроенных [триггеров и привязок](..\articles\azure-functions\functions-triggers-bindings.md) функций Azure
- Вызовы API базовых служб Azure
- Вызовы конечных точек RESTFUL
- Вызовы клиентских библиотек, пакетов или сторонних API

Чтобы избежать потери данных или пропущенных сообщений, необходимо выполнить следующие надежные методы обработки ошибок. Рекомендуемые методы обработки ошибок включают следующие действия.

- [Включить Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Использование структурированной обработки ошибок](#use-structured-error-handling)
- [Проектирование для идемпотентности](../articles/azure-functions/functions-idempotent.md)
- Реализация политик повтора (при необходимости)

### <a name="use-structured-error-handling"></a>Использование структурированной обработки ошибок

Запись и публикация ошибок очень важна для мониторинга работоспособности приложения. Самый верхний уровень кода функции должен включать блок try/catch. В блоке catch можно записывать и публиковать ошибки.

### <a name="retry-support"></a>Поддержка повторных попыток

Следующие триггеры поддерживают встроенный механизм повторных попыток:

* [Хранилище BLOB-объектов Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [хранилище очередей Azure](../articles/azure-functions/functions-bindings-storage-queue.md);
* [служебная шина Azure (очередь/тема)](../articles/azure-functions/functions-bindings-service-bus.md).

По умолчанию эти запросы повторно поступают в пять раз. После пятой попытки оба триггера записывают сообщение в [очередь подозрительных](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)сообщений.

Необходимо вручную реализовать политики повтора для любых других триггеров или типов привязок. Ручные реализации могут включать запись сведений об ошибках в [очередь подозрительных сообщений](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Написав в очередь подозрительных сообщений, вы сможете повторить операции позже. Этот подход аналогичен тому, который используется триггером хранилища BLOB-объектов.

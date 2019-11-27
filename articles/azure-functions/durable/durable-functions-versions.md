---
title: 'Обзор Устойчивые функции версий: функции Azure'
description: Дополнительные сведения о Устойчивые функции версиях.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 93c35eb4f69cc4f9b16f669d96c2df53f50bcf84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231194"
---
# <a name="durable-functions-versions-overview"></a>Обзор версий Устойчивые функции

*Устойчивые функции* являются расширением [Функций Azure](../functions-overview.md) и [веб-заданий Azure](../../app-service/web-sites-create-web-jobs.md), которое позволяет писать функции с отслеживанием состояния в безсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается. Если вы еще не знакомы с Устойчивые функции, см. [обзорную документацию](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Новые возможности в 2. x

В этом разделе описаны функции Устойчивые функции, добавленные в версии 2. x.

### <a name="durable-entities"></a>Устойчивые сущности

В Устойчивые функции 2. x мы предоставили новую концепцию [функций сущностей](durable-functions-entities.md) .

Функции сущностей определяют операции чтения и обновления мелких частей состояния, известных как *устойчивые сущности*. Как и функции оркестратора, функции сущностей — это функции с особым типом триггера, *триггером сущности*. В отличие от функций Orchestrator, функции сущностей не имеют конкретных ограничений кода. Функции сущностей также управляют состоянием явным образом, а не неявно представляют состояние с помощью потока управления.

Дополнительные сведения см. в статье о [устойчивых сущностях](durable-functions-entities.md) .

### <a name="durable-http"></a>Устойчивый протокол HTTP

В Устойчивые функции 2. x мы предоставили новую [устойчивую функцию HTTP](durable-functions-http-features.md#consuming-http-apis) , которая позволяет:

* Вызывайте API HTTP непосредственно из функций оркестрации (с некоторыми задокументированными ограничениями).
* Реализация автоматического опроса состояния HTTP 202 на стороне клиента.
* Встроенная поддержка [управляемых удостоверений Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Дополнительные сведения см. в статье о [функциях HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Миграция с 1. x на 2. x

В этом разделе описано, как перенести существующую версию 1. x Устойчивые функции в версию 2. x, чтобы воспользоваться преимуществами новых функций.

### <a name="upgrade-the-extension"></a>Обновление расширения

Установите версию 2. x [расширения привязок устойчивые функции](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) в проекте. Дополнительные сведения см. в разделе [Регистрация расширений привязки функций Azure](../functions-bindings-register.md) .

### <a name="update-your-code"></a>Обновление кода

Устойчивые функции 2. x вводит несколько критических изменений. Приложения Устойчивые функции 1. x несовместимы с Устойчивые функции 2. x без изменений кода. В этом разделе перечислены некоторые изменения, которые необходимо внести при обновлении функций версии 1. x до 2. x.

#### <a name="hostjson-schema"></a>Схема Host. JSON

Устойчивые функции 2. x использует новую схему Host. JSON. Основные изменения из 1. x включают:

* `"storageProvider"` (и подраздела `"azureStorage"`) для конфигурации, относящейся к хранилищу.
* `"tracking"` для настройки отслеживания и ведения журнала.
* `"notifications"` (и подраздела `"eventGrid"`) для конфигурации уведомлений сетки событий.

Дополнительные сведения см. в [справочной документации по устойчивые функции Host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="public-interface-changes-net-only"></a>Изменения открытого интерфейса (только .NET)

В версии 1. x различные объекты _контекста_ , поддерживаемые устойчивые функции, имеют абстрактные базовые классы, предназначенные для использования в модульном тестировании. В состав Устойчивые функции 2. x эти абстрактные базовые классы заменяются интерфейсами.

В следующей таблице представлены основные изменения.

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` или `IDurableClient` |
| `DurableOrchestrationContext` или `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` или `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

В случае, когда абстрактный базовый класс содержал виртуальные методы, эти виртуальные методы были заменены методами расширения, определенными в `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>изменения Function. JSON (JavaScript и C# скрипт)

В Устойчивые функции 1. x привязка клиента оркестрации использует `type` `orchestrationClient`. В версии 2. x вместо нее используется `durableClient`.

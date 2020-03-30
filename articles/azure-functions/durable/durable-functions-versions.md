---
title: Обзор версий прочных функций - Функции Azure
description: Узнайте больше о версиях функций длительного пользования.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152896"
---
# <a name="durable-functions-versions-overview"></a>Обзор версий долгосрочных функций

*Устойчивые функции* являются расширением [Функций Azure](../functions-overview.md) и [веб-заданий Azure](../../app-service/web-sites-create-web-jobs.md), которое позволяет писать функции с отслеживанием состояния в безсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается. Если вы еще не знакомы с [overview documentation](durable-functions-overview.md)функциями длительного пользования, см.

## <a name="new-features-in-2x"></a>Новые функции в 2.x

В этом разделе описаны функции функций длительного пользования, которые добавляются в версию 2.x.

### <a name="durable-entities"></a>Долгосрочные объекты

В компании Durable Functions 2.x мы представили новую концепцию [функциональных функций сущности.](durable-functions-entities.md)

Функции сущностей определяют операции чтения и обновления мелких частей состояния, известных как *устойчивые сущности*. Как и функции оркестратора, функции сущностей — это функции с особым типом триггера, *триггером сущности*. В отличие от функций оркестратора, функции сущности не имеют каких-либо конкретных ограничений кода. Функции сущностей также управляют состоянием явным образом, а не неявно представляют состояние с помощью потока управления.

Чтобы узнать больше, [см.](durable-functions-entities.md)

### <a name="durable-http"></a>Прочный HTTP

В функции durable 2.x мы представили новую функцию [Durable HTTP,](durable-functions-http-features.md#consuming-http-apis) которая позволяет:

* Вызов http AIS непосредственно из функций оркестровки (с некоторыми задокументированными ограничениями).
* Реализация автоматического опроса статуса клиента на стороне клиента HTTP 202.
* Встроенная поддержка [управляемых идентификаторов Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Чтобы узнать больше, смотрите статью [http функций.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Мигрировать с 1,x до 2,x

В этом разделе описывается, как перенести существующую версию 1.x Долгосрочные функции в версию 2.x, чтобы воспользоваться преимуществами новых функций.

### <a name="upgrade-the-extension"></a>Обновление расширения

Установите версию 2.x [расширения привязок прочных функций](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) в проекте. Для получения дополнительной информации смотрите [обязательные расширения Регистрации функций Azure.](../functions-bindings-register.md)

### <a name="update-your-code"></a>Обновление кода

Прочная функция 2.x вводит несколько изменений. Приложения Durable Functions 1.x не совместимы с durable Functions 2.x без изменений кода. В этом разделе перечислены некоторые изменения, которые необходимо внести при обновлении функций версии 1.x до 2.x.

#### <a name="hostjson-schema"></a>Схема Host.json

Компания Durable Functions 2.x использует новую схему host.json. Основные изменения от 1.x включают в себя:

* `"storageProvider"`(и `"azureStorage"` подраздел) для конфигурации для хранения данных.
* `"tracing"`для отслеживания и конфигурации регистрации.
* `"notifications"`(и `"eventGrid"` подраздел) для конфигурации уведомления сетки событий.

Подробную информацию можно узнать [из справочной документации Durable Functions host.json.](durable-functions-bindings.md#durable-functions-2-0-host-json)

#### <a name="default-taskhub-name-changes"></a>Изменения имени taskhub по умолчанию

В версии 1.x, если имя концентратора задач не было указано в host.json, оно было дефолтом на "DurableNotHub". В версии 2.x имя концентратора задач по умолчанию теперь происходит от имени приложения функции. Из-за этого, если вы не указали имя концентратора задач при обновлении до 2.x, ваш код будет работать с новым концентратором задач, и все в полете оркестровки больше не будут иметь приложение, обрабатывающее их. Для решения этой задачи можно либо явно установить имя концентратора задач на v1.x по умолчанию "DurableFunctionsHub", либо следовать нашим рекомендациям по [развертыванию с нулевым временем](durable-functions-zero-downtime-deployment.md) для получения подробной информации о том, как обрабатывать изменения, связанные с нарушением в полете, для оркестровок в полете.

#### <a name="public-interface-changes-net-only"></a>Изменения публичного интерфейса (только.NET)

В версии 1.x различные _контекстные_ объекты, поддерживаемые Durable Functions, имеют абстрактные базовые классы, предназначенные для использования в модульном тестировании. В рамках функций Durable 2.x эти абстрактные базовые классы заменяются интерфейсами.

В следующей таблице представлены основные изменения:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` либо `IDurableClient` |
| `DurableOrchestrationContext` либо `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` либо `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

В случае, когда абстрактный базовый класс содержал виртуальные методы, `DurableContextExtensions`эти виртуальные методы были заменены методами расширения, определенными в .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json изменения (JavaScript и СИ-скрипт)

В функции durable 1.x связывание `type` `orchestrationClient`клиента-оркестра использует a. Вместо этого используется `durableClient` версия 2.x.

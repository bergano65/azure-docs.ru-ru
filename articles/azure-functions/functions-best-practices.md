---
title: Рекомендации по Функциям Azure
description: Ознакомьтесь с рекомендациями и шаблонами для Функций Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227385"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Оптимизация производительности и надежности Функций Azure

В этой статье описано, как повысить производительность и надежность [бессерверных](https://azure.microsoft.com/solutions/serverless/) приложений-функций.  

## <a name="general-best-practices"></a>Общие рекомендации

Ниже приведены рекомендации по созданию и разработке бессерверных решений с помощью службы "Функции Azure".

### <a name="avoid-long-running-functions"></a>Избегайте длительных функций

Крупные длительные функции могут вызывать непредвиденные проблемы времени ожидания. To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. Импорт зависимостей может также привести к замедлению загрузки, что, в свою очередь, приводит к непредвиденным проблемам времени ожидания. Зависимости можно загрузить явно и неявно. Один модуль, загруженный в коде, может загрузить собственные дополнительные модули. 

По возможности выполняйте рефакторинг крупных функций и перерабатывайте их на более мелкие совместимые наборы функций, которые работают сообща и быстро возвращают ответ. For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. Полезные данные триггера HTTP можно передать в очередь для обработки с помощью функции триггера очереди. This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>Взаимодействие функций

[Устойчивые функции](durable/durable-functions-overview.md) и [Azure Logic Apps](../logic-apps/logic-apps-overview.md) используются для управления переходами состояний и обмена данными между несколькими функциями.

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

Размер отдельных сообщений в очереди хранилища ограничен до 64 КБ. Если между функциями нужно передать сообщения большего размера, можно использовать очередь служебной шины Azure, которая поддерживает сообщения размером до 256 КБ на уровне "Стандартный" и 1 МБ на уровне "Премиум".

Если перед обработкой сообщений их нужно отфильтровать, ознакомьтесь со статьями о служебной шине.

Для поддержки обмена крупными сообщениями используются Центры событий.


### <a name="write-functions-to-be-stateless"></a>Создавайте функции без отслеживания состояния 

По возможности функции должны быть без отслеживания состояния и идемпотентными. Свяжите любые необходимые сведения о состоянии со своими данными. Например, с обрабатываемым заказом скорее всего будет связан элемент `state`. Функция может обработать заказ, основываясь на этом состоянии, но в ней самой при этом не отслеживается состояние. 

Идемпотентные функции рекомендуется использовать с триггерами таймера. For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. Кроме того, если предыдущее выполнение завершилось ошибкой, следующее выполнение должно начаться с прерванного момента.


### <a name="write-defensive-functions"></a>Создавайте защищенные функции

Предположим, что в любое время в функции может возникнуть исключение. Реализуйте в функции возможность продолжения с предыдущей точки сбоя во время следующего выполнения. Давайте рассмотрим сценарий, в котором необходимо сделать следующее:

1. Query for 10,000 rows in a database.
2. Создать сообщение очереди для каждой из этих строк для дальнейшей обработки.
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. Функции необходимо подготавливать к таким проблемам.

Как отреагирует ваш код при сбое после вставки 5000 элементов в очередь для обработки? Отслеживайте элементы в наборе, работа с которым завершена. В противном случае их можно вставить позже. This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

Если элемент очереди уже обработан, разрешите холостой цикл выполнения функции.

Воспользуйтесь предоставленными возможностями защиты для компонентов, используемых на платформе Функций Azure. Например, ознакомьтесь с разделом **Обработка подозрительных сообщений очереди** в документации по [триггерам и привязкам очереди службы хранилища Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Рекомендации по масштабируемости

There are a number of factors that impact how instances of your function app scale. Дополнительные сведения см. в документации по [масштабированию функций](functions-scale.md).  Ниже приведены рекомендации по оптимальному масштабированию приложения-функции.

### <a name="share-and-manage-connections"></a>Управление подключениями и общий доступ к ним

Reuse connections to external resources whenever possible.  См. раздел [Способы управления подключениями в службе "Функции Azure"](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Не используйте тестовый и рабочий код в одном приложении-функции

Функции в приложении-функции совместно используют ресурсы. Например, память. Если приложение-функция используется в рабочей среде, не добавляйте в нее тестовые функции и ресурсы. Это может вызвать непредвиденные затраты во время выполнения кода в рабочей среде.

Следите за тем, что вы загружаете в рабочие приложения-функции. Память усредняется для каждой функции в приложении.

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Использование асинхронного кода без блокирующих вызовов

Рекомендуется применять метод асинхронного программирования. Но никогда не используйте ссылки на свойство `Result` и не вызывайте метод `Wait` для экземпляра `Task`. Применение этого подхода может привести к нехватке потоков.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>По возможности получайте сообщения в пакетном режиме

Некоторые триггеры, например триггер концентратора событий, позволяют получать сообщения в пакетном режиме в рамках одного вызова.  Пакетная обработка сообщений обеспечивает более высокую производительность.  Вы можете настроить максимальный размер пакета в файле `host.json`, как описано в [справочной документации по host.json](functions-host-json.md).

For C# functions, you can change the type to a strongly-typed array.  Например, вместо `EventData sensorEvent` можно использовать сигнатуру метода `EventData[] sensorEvent`.  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Настройте поведение узла для обеспечения оптимального параллелизма

Файл `host.json` в приложении-функции позволяет настраивать среду выполнения узла и поведение триггера.  Кроме настройки поведения пакетной обработки, вы можете управлять параллелизмом определенного числа триггеров. Часто настройка этих параметров помогает масштабировать каждый экземпляр согласно требованиям вызванных функций.

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Способы управления подключениями в службе "Функции Azure"](manage-connections.md)
* [Рекомендации по использованию службы приложений Azure](../app-service/app-service-best-practices.md)

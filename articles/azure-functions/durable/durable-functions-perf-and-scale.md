---
title: Производительность и масштабирование в устойчивых функциях — Azure
description: Общие сведения о расширении устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 15302eb4f89c854210d4fc1aba292c57d4757278
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231340"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Производительность и масштабирование в устойчивых функциях (Функции Azure)

Чтобы оптимизировать производительность и масштабирование, важно понять уникальные характеристики масштабирования [устойчивых функций](durable-functions-overview.md).

Чтобы понять реакции на события масштабирования, необходимо изучить некоторые сведения о базовом поставщике службы хранилища Azure.

## <a name="history-table"></a>Таблица журнала

Таблица **журнала** — это таблица службы хранилища Azure, содержащая события журнала для всех экземпляров оркестрации внутри центра задач. Имя этой таблицы — указывается в формате журнала *TaskHubName*. При выполнении экземпляров в таблицу добавляются новые строки. Ключ раздела этой таблицы является производным от идентификатора экземпляра оркестрации. В большинстве случаев экземпляр идентификатора является случайным, что обеспечивает оптимальное распределение внутренних разделов в службе хранилища Azure.

Когда необходимо выполнить экземпляр оркестрации, в память загружаются нужные строки таблицы журнала. Затем эти *события журнала* воспроизводятся в коде функции оркестратора, чтобы вернуть их в предыдущее состояние из контрольной точки. Использование журнала выполнения для перестройки состояния, является путем, обусловленным [шаблоном источников событий](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Экземпляры таблицы

The **Instances** table is another Azure Storage table that contains the statuses of all orchestration and entity instances within a task hub. При создании экземпляров в таблицу добавляются новые строки. The partition key of this table is the orchestration instance ID or entity key and the row key is a fixed constant. There is one row per orchestration or entity instance.

This table is used to satisfy instance query requests from the `GetStatusAsync` (.NET) and `getStatus` (JavaScript) APIs as well as the [status query HTTP API](durable-functions-http-api.md#get-instance-status). В конечном итоге она поддерживается в соответствии с содержанием таблицы **Журнала**, упомянутой ранее. Использование отдельных таблиц хранилища Azure для эффективного удовлетворения операций запроса экземпляров, является путем, обусловленным [шаблоном CQRS](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Триггеры внутренней очереди

Функции оркестратора и действия активируются внутренними очередями в учетной записи хранения центра задач приложения-функции. Такое использование очередей обеспечивает надежные гарантии доставки сообщений, как минимум один раз. Существует два типа очередей в устойчивых функциях: очередь **управления** и **очередь рабочих элементов**.

### <a name="the-work-item-queue"></a>Очередь рабочих элементов

В устойчивых функциях на каждый центр задач приходится одна очередь рабочих элементов. Это основная очередь, и ее поведение аналогично любым другим очередям `queueTrigger` в Функциях Azure. Эта очередь используется для активации *функций действий* путем вывода только одного сообщения за один раз. Каждое из этих сообщений содержит входные данные функции действия и дополнительные метаданные, например, какую функцию выполнять. Когда приложение устойчивых функций масштабируется до нескольких виртуальных машин, все они конкурируют за получение работы из очереди рабочих элементов.

### <a name="control-queues"></a>Очередь управления

В устойчивых функциях на каждый центр задач имеется несколько *очередей управления*. *Очередь управления* является более сложной, чем очередь рабочих элементов. Control queues are used to trigger the stateful orchestrator and entity functions. Because the orchestrator and entity function instances are stateful singletons, it's not possible to use a competing consumer model to distribute load across VMs. Instead, orchestrator and entity messages are load-balanced across the control queues. Дополнительные сведения об этом поведении можно найти в последующих разделах.

Очереди управления содержат различные типы сообщений жизненного цикла оркестрации. Примеры включают [сообщения об управлении оркестрацией](durable-functions-instance-management.md), сообщения *ответа* функции действия и сообщения таймера. За один опрос из очереди управления может быть удалено до 32 сообщений. Эти сообщения содержат полезные данные, а также метаданные, включая экземпляр оркестрации, для которого они предназначены. Если для одного и того же экземпляра оркестрации предназначены несколько сообщений удаленных из очереди, они будут обрабатываться как пакет.

### <a name="queue-polling"></a>Queue polling

The durable task extension implements a random exponential back-off algorithm to reduce the effect of idle-queue polling on storage transaction costs. When a message is found, the runtime immediately checks for another message; when no message is found, it waits for a period of time before trying again. After subsequent failed attempts to get a queue message, the wait time continues to increase until it reaches the maximum wait time, which defaults to 30 seconds.

The maximum polling delay is configurable via the `maxQueuePollingInterval` property in the [host.json file](../functions-host-json.md#durabletask). Setting this property to a higher value could result in higher message processing latencies. Higher latencies would be expected only after periods of inactivity. Setting this property to a lower value could result in higher storage costs due to increased storage transactions.

> [!NOTE]
> When running in the Azure Functions Consumption and Premium plans, the [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) will poll each control and work-item queue once every 10 seconds. This additional polling is necessary to determine when to activate function app instances and to make scale decisions. At the time of writing, this 10 second interval is constant and cannot be configured.

## <a name="storage-account-selection"></a>Выбор учетной записи хранения

The queues, tables, and blobs used by Durable Functions are created in a configured Azure Storage account. The account to use can be specified using the `durableTask/storageProvider/connectionStringName` setting (or `durableTask/azureStorageConnectionStringName` setting in Durable Functions 1.x) in the **host.json** file.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Если значение не указано, учетная запись `AzureWebJobsStorage` используется по умолчанию. Однако, для рабочих нагрузок, требующих высокой производительности, рекомендуется настроить учетную запись хранения, кроме используемой по умолчанию. Устойчивые функции сильно полагаются на хранилище Azure и используют отдельную учетную запись хранения для изоляции использования хранилища устойчивых функций узлом функций Azure.

## <a name="orchestrator-scale-out"></a>Развертывание оркестратора

Функции действий не отслеживают состояние и автоматически масштабируются путем добавления виртуальных машин. Orchestrator functions and entities, on the other hand, are *partitioned* across one or more control queues. Число очередей управления определено в файле **host.json**. The following example host.json snippet sets the `durableTask/storageProvider/partitionCount` property (or `durableTask/partitionCount` in Durable Functions 1.x) to `3`.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Центр задач может быть настроен в диапазоне от 1 до 16 секций. Если значение не задано, по умолчанию количеству секций соответствует значение **4**.

При развертывании нескольких экземпляров узлов функции (обычно на разных виртуальных машинах) каждый экземпляр блокируется на одной из очередей управления. These locks are internally implemented as blob storage leases and ensure that an orchestration instance or entity only runs on a single host instance at a time. If a task hub is configured with three control queues, orchestration instances and entities can be load-balanced across as many as three VMs. Для повышения емкости выполнения функции действия можно добавить дополнительные виртуальные машины.

На схеме ниже показано взаимодействие узла Функций Azure с сущностями хранилища в развернутой среде.

![Схема масштабирования](./media/durable-functions-perf-and-scale/scale-diagram.png)

Как показано на предыдущей схеме, все виртуальные машины могут конкурировать за сообщения в очереди рабочих элементов. Тем не менее получать сообщения из очередей управления могут только три виртуальные машины, и каждая из них блокирует одиночную очередь управления.

Orchestration instances and entities are distributed across all control queue instances. The distribution is done by hashing the instance ID of the orchestration or the entity name and key pair. Orchestration instance IDs by default are random GUIDs, ensuring that instances are equally distributed across all control queues.

Как правило, функции оркестратора должны быть упрощенными, поэтому они не должны требовать больших вычислительных мощностей. It is therefore not necessary to create a large number of control queue partitions to get great throughput for orchestrations. Большая часть работы должна выполняться в функциях действий без отслеживания состояния, которые можно масштабировать бесконечно.

## <a name="auto-scale"></a>Автомасштабирование

As with all Azure Functions running in the Consumption and Elastic Premium plans, Durable Functions supports auto-scale via the [Azure Functions scale controller](../functions-scale.md#runtime-scaling). Контроллер масштабирования занимается мониторингом задержки всех очередей, периодически выполняя команды _peek_. Основываясь на задержках просматриваемых сообщений, контроллер масштабирования будет решать, следует ли добавлять или удалять виртуальные машины.

Если контроллер масштабирования определит что задержка от сообщения очереди управления слишком большая, он будет увеличивать количество экземпляров виртуальных машин пока она не вернется до приемлемого уровня или достигнет максимального количества разделов очереди управления. Аналогичным образом контроллер масштабирования будет непрерывно добавлять экземпляры виртуальных машин в случае если задержки в очереди на рабочих элементах слишком большие, независимо от количества разделов.

> [!NOTE]
> Starting with Durable Functions 2.0, function apps can be configured to run within VNET-protected service endpoints in the Elastic Premium plan. In this configuration, the Durable Functions triggers initiate scale requests instead of the Scale Controller.

## <a name="thread-usage"></a>Использование потока

Функции оркестратора выполняются в одном потоке, чтобы убедится, что выполнение можно детерминировать на множество воспроизведений. Так как выполнение является однопоточным, очень важно, чтобы потоки функции оркестратора по любой причине не выполняли задачи, интенсивно использующие ЦП, операции ввода-вывода или блокировку. Все операции, которым может потребоваться ввод-вывод, блокировка или несколько потоков, должны выполняться с помощью функций действий.

Функции действий имеют такие же реакции на события, как и регулярные активируемые очередью функции. Они могут безопасно выполнять операции ввода-вывода, операции с интенсивным потреблением ЦП и использовать несколько потоков. Так как триггеры действия не отслеживают состояние, они могут свободно масштабироваться на неограниченное количество виртуальных машин.

Entity functions are also executed on a single thread and operations are processed one-at-a-time. However, entity functions do not have any restrictions on the type of code that can be executed.

## <a name="concurrency-throttles"></a>Регулирование параллелизма

Функции Azure поддерживают параллельное выполнение нескольких функций в рамках одного экземпляра приложения. Параллельное выполнение помогает улучшить параллелизм и минимизировать число холодного запуска, с которым со времени столкнется стандартное приложение. However, high concurrency can exhaust per-VM system resources such network connections or available memory. В зависимости от потребностей приложения функции, может потребоваться отрегулировать параллелизм для каждого экземпляра, чтобы избежать возможности исчерпания памяти в ситуациях с высокой нагрузкой.

Activity, orchestrator, and entity function concurrency limits can be configured in the **host.json** file. The relevant settings are `durableTask/maxConcurrentActivityFunctions` for activity functions and `durableTask/maxConcurrentOrchestratorFunctions` for both orchestrator and entity functions.

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Функции 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

In the previous example, a maximum of 10 orchestrator or entity functions and 10 activity functions can run on a single VM concurrently. If not specified, the number of concurrent activity and orchestrator or entity function executions is capped at 10X the number of cores on the VM.

> [!NOTE]
> Эти параметры полезны для управления памятью и использованием ЦП на одной виртуальной машине. However, when scaled out across multiple VMs, each VM has its own set of limits. These settings can't be used to control concurrency at a global level.

## <a name="extended-sessions"></a>Extended sessions

Extended sessions is a setting that keeps orchestrations and entities in memory even after they finish processing messages. Распространенным эффектом от включения расширенных сеансов является сокращение операций ввода-вывода с использованием учетной записи службы хранения Azure и повсеместное улучшение пропускной способности.

You can enable extended sessions by setting `durableTask/extendedSessionsEnabled` to `true` in the **host.json** file. The `durableTask/extendedSessionIdleTimeoutInSeconds` setting can be used to control how long an idle session will be held in memory:

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

There are two potential downsides of this setting to be aware of:

1. There's an overall increase in function app memory usage.
2. There can be an overall decrease in throughput if there are many concurrent, short-lived orchestrator or entity function executions.

As an example, if `durableTask/extendedSessionIdleTimeoutInSeconds` is set to 30 seconds, then a short-lived orchestrator or entity function episode that executes in less than 1 second still occupies memory for 30 seconds. It also counts against the `durableTask/maxConcurrentOrchestratorFunctions` quota mentioned previously, potentially preventing other orchestrator or entity functions from running.

The specific effects of extended sessions on orchestrator and entity functions are described in the next sections.

### <a name="orchestrator-function-replay"></a>Повторение функции оркестратора

Как было упомянуто ранее, функции оркестратора воспроизводятся с помощью содержимого таблицы **Журнал**. По умолчанию код функции оркестратора воспроизводится каждый раз, когда пакет сообщений удаляется из очереди управления. When extended sessions are enabled, orchestrator function instances are held in memory longer and new messages can be processed without a full history replay.

The performance improvement of extended sessions is most often observed in the following situations:

* When there are a limited number of orchestration instances running concurrently.
* When orchestrations have large number of sequential actions (e.g. hundreds of activity function calls) that complete quickly.
* When orchestrations fan-out and fan-in a large number of actions that complete around the same time.
* When orchestrator functions need to process large messages or do any CPU-intensive data processing.

In all other situations, there is typically no observable performance improvement for orchestrator functions.

> [!NOTE]
> Эти параметры следует использовать только после того, как функция оркестратора будет полностью разработана и протестирована. The default aggressive replay behavior can useful for detecting [orchestrator function code constraints](durable-functions-code-constraints.md) violations at development time, and is therefore disabled by default.

### <a name="entity-function-unloading"></a>Entity function unloading

Entity functions process up to 20 operations in a single batch. As soon as an entity finishes processing a batch of operations, it persists its state and unloads from memory. You can delay the unloading of entities from memory using the extended sessions setting. Entities continue to persist their state changes as before, but remain in memory for the configured period of time to reduce the number of loads from Azure Storage. This reduction of loads from Azure Storage can improve the overall throughput of frequently accessed entities.

## <a name="performance-targets"></a>Цели анализа производительности

Планируя использовать устойчивые функции в рабочем приложении, на раннем этапе планирования необходимо учесть требования к производительности. В этом разделе рассматриваются некоторое основные сценарии использования и ожидаемое максимальной число пропускной способности.

* **Выполнение последовательного действия**. В этом сценарии описывается функция оркестратора, которая по очереди выполняет ряд функций действия. Максимально похожим на этот сценарий является [цепочка функции](durable-functions-sequence.md).
* **Параллельное выполнение одного действия**. В этом сценарии описано действие оркестратора, которое выполняет множество функций действий параллельно, с помощью шаблона [развертывания, слияния](durable-functions-cloud-backup.md).
* **Параллельная обработка ответа**. Этот сценарий является второй половиной шаблона [развертывания, слияния](durable-functions-cloud-backup.md). Этот раздел посвящен производительность слияния. Важно отметить, что в отличие от развертывания, слияние создается единым экземпляром функции а затем может быть запущено на единой виртуальной машине.
* **Обработка внешних событий**. В этом сценарии представлено единый экземпляр функции оркестратора, который последовательно ожидает [внешних событий](durable-functions-external-events.md).
* **Entity operation processing**: This scenario tests how quickly a _single_ [Counter entity](durable-functions-entities.md) can process a constant stream of operations.

> [!TIP]
> В отличие от развертывания, операции слияния могут размещаться только на одной виртуальной машине. Если ваше приложение использует шаблоны развертывания и слияния, и вы беспокоитесь через производительность развертывания, необходимо рассмотреть разделение действия функции развертывания на несколько [вложенных оркестраций](durable-functions-sub-orchestrations.md).

В следующей таблице показаны ожидаемые *максимальные* показателей пропускной способности для сценариев, описанных ранее. Термин "экземпляр" относиться к единому экземпляру функции оркестратора, запущенной на небольшом экземпляре виртуальной машины ([серии A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) в службе приложений Azure. Во всех случаях предполагается, что [расширенные сеансы](#orchestrator-function-replay) включены. Фактические результаты могут варьировать, в зависимости от ЦП или ввода-вывода, выполняемых кодом функции.

| Сценарий | Максимальная пропускная способность |
|-|-|
| Выполнение последовательного действия | 5 действий на экземпляр в секунду |
| Параллельное выполнение одного действия (при развертывании) | 100 действий на экземпляр в секунду |
| Параллельная обработка ответа (при слиянии) | 150 ответов на экземпляр в секунду |
| Обработка внешних событий | 50 событий на экземпляр в секунду |
| Entity operation processing | 64 operations per second |

> [!NOTE]
> Эти номера являются текущими по сравнению с выпуском расширения устойчивых функций v1.4.0 (GA). Со временем эти числа могут изменятся (по мере роста функции и оптимизации).

Если вы не видите показателей пропускной способности, и ваш ЦП и использование памяти отображаются в работоспособном состоянии, проверьте, описаны ли эти случаи в разделе [рекомендации по устранению неполадок](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Расширение устойчивых функций может существенно увеличить нагрузку на учетную запись службы хранения Azure, что при достаточно высоких загрузках может привести к регулированию учетной записи.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Learn about disaster recovery and geo-distribution](durable-functions-disaster-recovery-geo-distribution.md)

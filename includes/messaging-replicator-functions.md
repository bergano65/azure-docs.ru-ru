---
title: включить файл
description: включить файл
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 279a00a6146d756e6a518dbf86b88f471d170b3a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805669"
---
## <a name="what-is-a-replication-task"></a>Что такое задача репликации?

Задача репликации получает события из источника и перенаправляет их в целевой объект.
Большинство задач репликации пересылают события без изменений и чаще всего выполняют сопоставление структур метаданных, если исходный и целевой протоколы различаются. 

Задачи репликации обычно не имеют состояния. Это означает, что они не имеют общего состояния или другие побочные эффекты в последовательных или параллельных выполнениях задачи. Это также справедливо для пакетной обработки и объединения цепочек, которые могут быть реализованы поверх существующего состояния потока. 

Это делает задачи репликации отличными от статистических задач, которые обычно имеют состояние, а также представляют собой доменные платформы и службы аналитики, такие как [Azure Stream Analytics](/azure/stream-analytics/stream-analytics-introduction).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Приложения и задачи репликации в функциях Azure

В функциях Azure задача репликации реализуется с помощью [триггера](/azure/azure-functions/functions-triggers-bindings) , который получает одно или несколько входных сообщений из настроенного источника и [выходную привязку](/azure/azure-functions/functions-triggers-bindings#binding-direction) , которая перенаправляет сообщения, скопированные из источника в настроенный целевой объект. 

| Триггер  | Выходные данные |
|----------|--------|
| [Триггер концентраторов событий Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Выходная привязка концентраторов событий Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Триггер служебной шины Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Выходная привязка служебной шины Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Триггер центра Интернета вещей Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Выходная привязка центра Интернета вещей Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Триггер службы "Сетка событий Azure"](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Выходная привязка сетки событий Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Триггер хранилища очередей Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Выходная привязка хранилища очередей Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Триггер Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Выходная привязка Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Триггер RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Выходная привязка RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Выходная привязка центров уведомлений Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Выходная привязка службы SignalR Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Выходная привязка Twilio SendGrid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

Задачи репликации развертываются в приложении репликации с помощью тех же методов развертывания, что и любое другое приложение функций Azure. В одном приложении можно настроить несколько задач. 

С помощью функций Azure Premium несколько приложений репликации могут совместно использовать один и тот же базовый пул ресурсов, называемый планом службы приложений. Это означает, что можно легко разместить задачи репликации, написанные на .NET, с задачами репликации, которые написаны на Java. Это имеет значение, если вы хотите воспользоваться преимуществами определенных библиотек, таких как Apache Camel, которые доступны только для Java, и если это лучший вариант для конкретного пути интеграции, хотя для других задач репликации обычно предпочитается другой язык и среда выполнения. 

Каждый раз, когда он доступен, предпочтительно использовать триггеры, ориентированные на пакетные функции, по триггерам, которые доставляют отдельные события или сообщения, и всегда должны получать полную структуру события или сообщения, а не полагаться на [выражения привязки параметров](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)Azure.

Имя функции должно отражать пару источника и целевого объекта, к которому выполняется подключение, а также префиксные ссылки на строки подключения или другие элементы конфигурации в файлах конфигурации приложения с таким именем. 

### <a name="data-and-metadata-mapping"></a>Сопоставление данных и метаданных

Когда вы решили пару входных триггеров и выходных данных, необходимо выполнить определенное сопоставление между различными типами событий или сообщений, если только тип триггера и результат не совпадают.

Для простых задач репликации, которые копируют сообщения между концентраторами событий и служебной шиной, не нужно писать собственный код, но можно использовать [библиотеку служебной программы, предоставляемую](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) с примерами репликации.

### <a name="retry-policy"></a>Политика повтора

Чтобы избежать потери данных во время события доступности с обеих сторон функции репликации, необходимо настроить политику повтора, чтобы она была надежной. Дополнительные сведения о настройке политики повторных попыток см. в [документации по функциям Azure](/azure/azure-functions/functions-bindings-error-pages) . 

Параметры политики, выбранные для проектов примера в примере [репозитория](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) , настраивают стратегию экспоненциальной задержки с интервалами повтора от 5 секунд до 15 минут с бесконечными повторными попытками, чтобы избежать потери данных. 

Для служебной шины ознакомьтесь с разделом ["использование поддержки повторов на основе устойчивости триггеров"](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) , чтобы понять взаимодействие триггеров и максимальное число доставок, определенное для очереди.

### <a name="setting-up-a-replication-application-host"></a>Настройка узла приложения репликации

Приложение репликации — это узел выполнения для одной или нескольких задач репликации. 

Это приложение функций Azure, которое настроено для запуска либо в плане потребления, либо (рекомендуется) для плана функций Azure Premium. Все приложения репликации должны выполняться с [управляемым удостоверением, назначенным системой или пользователем](/azure/app-service/overview-managed-identity). 

Связанные шаблоны Azure Resource Manager (ARM) создают и настраивают приложение репликации с помощью:

* Учетная запись хранения Azure для отслеживания хода выполнения репликации и журналов,
* Управляемое системой удостоверение и 
* Мониторинг Azure и Application Insights интеграция для мониторинга.

Приложения репликации, которые должны получить доступ к концентраторам событий, связанным с виртуальной сетью Azure, должны использовать план "функции Azure" Premium и быть настроены для подключения к той же виртуальной сети, которая также является одним из доступных вариантов.


|       | Развертывание | Визуализация  
|-------|------------------|--------------|---------------|
| **План потребления службы "Функции Azure"** | [![Развернуть в Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Визуализация](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **План функций Azure Premium** |[![Развернуть в Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Визуализация](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **План функций Azure Premium с виртуальной сетью** | [![Развернуть в Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Визуализация](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Примеры

[Репозиторий примеров](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) содержит несколько примеров задач репликации, которые копируют события между концентраторами событий и (или) между сущностями служебной шины.

Для копирования событий между концентраторами событий используется триггер концентратора событий с выходной привязкой концентратора событий:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Для копирования сообщений между сущностями служебной шины используется триггер и выходная привязка служебной шины.

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Вспомогательные методы могут упростить репликацию между концентраторами событий и служебной шиной:

| Источник      | Назначение      | Точка входа 
|-------------|-------------|------------------------------------------------------------------------
| Концентратор событий   | Концентратор событий   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Концентратор событий   | Служебная шина | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Служебная шина | Концентратор событий   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Служебная шина | Служебная шина | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Мониторинг

Дополнительные сведения о мониторинге приложения репликации см. в [разделе "Мониторинг](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) " в документации по функциям Azure.

Особенно полезным визуальным инструментом для наблюдения за задачами репликации является Application Insights [схема приложения](https://docs.microsoft.com/azure/azure-monitor/app/app-map), которая автоматически создается на основе захваченных данных мониторинга и позволяет изучить надежность и производительность источника задачи репликации и передачи целевых объектов.

Для немедленной диагностики можно работать с помощью средства портала [Live метрик](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) , которое обеспечивает визуализацию подробных сведений журнала с низкой задержкой.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывания функций Azure](/azure/azure-functions/functions-deployment-technologies)
* [Диагностика функций Azure](/azure/azure-functions/functions-diagnostics)
* [Параметры сети функций Azure](/azure/azure-functions/functions-networking-options)
* [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)
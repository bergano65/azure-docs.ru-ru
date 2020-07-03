---
title: Настройка журналов диагностики Центров событий Azure | Документация Майкрософт
description: Узнайте, как настроить журналы действий и журналы диагностики для Центров событий в Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691842"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Настройка журналов диагностики для центра событий Azure

Для Центров событий Azure можно просмотреть журналы двух типов.

* **[Журналы действий](../azure-monitor/platform/platform-logs-overview.md)**. Эти журналы содержат сведения об операциях, выполненных над заданием. Данные журналы всегда включены. Чтобы просмотреть записи журнала действий, выберите **Журнал действий** в левой области для пространства имен концентратора событий в портал Azure. Например: "Создание или обновление пространства имен", "Создание или обновление концентратора событий".

    ![Журнал действий для пространства имен концентраторов событий](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Журналы диагностики](../azure-monitor/platform/platform-logs-overview.md)**. журналы диагностики предоставляют более подробные сведения об операциях и действиях, выполняемых в пространстве имен с помощью API, или через клиентов управления на языке SDK. 
    
    В следующем разделе показано, как включить журналы диагностики для пространства имен концентраторов событий.

## <a name="enable-diagnostic-logs"></a>Включение журналов диагностики
По умолчанию журналы диагностики отключены. Чтобы включить журналы диагностики, сделайте следующее:

1.  В [портал Azure](https://portal.azure.com)перейдите к пространству имен концентраторов событий. 
2. Выберите **параметры диагностики** в разделе **мониторинг** на левой панели, а затем выберите **+ Добавить параметр диагностики**. 

    ![Страница "параметры диагностики" — Добавление параметра диагностики](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. В разделе **сведения о категории** выберите **типы журналов диагностики** , которые необходимо включить. Сведения об этих категориях см. Далее в этой статье. 
5. В разделе **сведения о назначении** задайте требуемый целевой объект архива (назначение). Например, учетная запись хранения, концентратор событий или Рабочая область Log Analytics.

    ![Страница добавления параметров диагностики](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Нажмите кнопку **сохранить** на панели инструментов, чтобы сохранить параметры диагностики.

    Новые параметры вступят в силу в течение 10 минут. После этого журналы появятся в настроенной цели для архивирования на панели **Журналы диагностики**.

    Дополнительные сведения о настройке системы диагностики доступны в [обзоре журналов диагностики Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Категории журналов диагностики

Концентраторы событий записывают журналы диагностики для следующих категорий:

| Категория | Описание | 
| -------- | ----------- | 
| Журналы архивации | Захватывает сведения об операциях [записи концентраторов событий](event-hubs-capture-overview.md) , в частности о журналах, связанных с ошибками записи. |
| Журналы операций | Запишите все операции управления, которые выполняются в пространстве имен концентраторов событий Azure. Операции с данными не фиксируются из-за большого объема операций с данными, проводимых в концентраторах событий Azure. |
| Автоматическое масштабирование журналов | Фиксирует операции автоматического расширения, выполненные в пространстве имен концентраторов событий. |
| Журналы координатора Kafka | Захватывает операции координатора Kafka, связанные с концентраторами событий. |
| Журналы ошибок пользователя Kafka | Захватывает сведения о API Kafka, которые вызываются в концентраторах событий. |
| Событие подключения виртуальной сети (VNet) концентраторов событий | Захватывает сведения об IP-адресах и виртуальных сетях, отправляющих трафик в концентраторы событий. |
| Пользовательские журналы с управляемым клиентом ключом | Фиксирует операции, связанные с ключом, управляемым клиентом. |


Все журналы хранятся в формате JSON (нотация объектов JavaScript). Каждая запись содержит строковые поля, использующие формат, описанный в следующих разделах.

## <a name="archive-logs-schema"></a>Схема архивных журналов

Строки JSON архивных журналов содержат элементы, перечисленные в следующей таблице.

Название | Описание
------- | -------
TaskName | Описание задачи, завершившейся сбоем.
ActivityId | Внутренний идентификатор, используемый для отслеживания
trackingId | Внутренний идентификатор, используемый для отслеживания
resourceId | Идентификатор ресурса Azure Resource Manager
eventHub | Полное имя концентратора событий (включает в себя имя пространства имен).
partitionId | Идет запись секции концентратора событий
archiveStep | возможные значения: Арчивефлушвритер, Дестинатионинит
startTime | Время начала сбоя
failures | Количество раз, когда произошел сбой
durationInSeconds | Продолжительность сбоя
сообщение | Сообщение об ошибке
категория | ArchiveLogs

Ниже приведен пример строки JSON журнала архивирования.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Схема операционных журналов

Строки JSON операционного журнала содержат элементы, перечисленные в приведенной ниже таблице.

Название | Описание
------- | -------
ActivityId | Внутренний идентификатор, используемый для отслеживания |
EventName | Имя операции |
resourceId | Идентификатор ресурса Azure Resource Manager |
SubscriptionId | Идентификатор подписки |
EventTimeString | Время операции |
EventProperties | Свойства операции |
Состояние | Состояние операции |
Caller | Объект, вызвавший операцию (портал Azure или клиент управления) |
Категория | OperationalLogs |

Ниже приведен пример строки JSON операционного журнала.

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Схема журналов автомасштабирования
Журнал автомасштабирования JSON включает элементы, перечисленные в следующей таблице.

| Название | Описание |
| ---- | ----------- | 
| TrackingId | Внутренний идентификатор, используемый для целей трассировки |
| ResourceId | Идентификатор ресурса Azure Resource Manager. |
| Сообщение | Информационное сообщение, содержащее сведения об автоматическом неструктурированном действии. Сообщение содержит предыдущее и текущее значение единицы пропускной способности для данного пространства имен, а также то, что привело к увеличению TU. |

## <a name="kafka-coordinator-logs-schema"></a>Схема журналов координатора Kafka
JSON журнала координатора Kafka включает элементы, перечисленные в следующей таблице.

| Название | Описание |
| ---- | ----------- | 
| RequestId | Идентификатор запроса, используемый для целей трассировки |
| ResourceId | Идентификатор ресурса Azure Resource Manager |
| Операция | Имя операции, выполняемой во время координации групп |
| ClientId | Идентификатор клиента |
| NamespaceName | Имя пространства имен | 
| SubscriptionId | Идентификатор подписки Azure. |
| Сообщение | Информационное сообщение или предупреждение, содержащее сведения о действиях, выполненных во время координации групп. |

### <a name="example"></a>Пример

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Схема журналов ошибок пользователя Kafka
Журнал ошибок пользователя Kafka. JSON содержит элементы, перечисленные в следующей таблице:

| Название | Описание |
| ---- | ----------- |
| TrackingId | ИДЕНТИФИКАТОР отслеживания, который используется для целей трассировки. |
| NamespaceName | Имя пространства имен |
| Eventhub | Имя концентратора событий |
| PartitionId | Partition ID |
| GroupId | Идентификатор группы |
| ClientId | Идентификатор клиента |
| ResourceId | Идентификатор ресурса Azure Resource Manager. |
| Сообщение | Информационное сообщение, содержащее сведения об ошибке |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Схема событий подключения виртуальной сети концентраторов событий

Событие подключения виртуальной сети концентраторов событий (VNet) содержит элементы, перечисленные в следующей таблице:

| Название | Описание |
| ---  | ----------- | 
| SubscriptionId | Идентификатор подписки Azure. |
| NamespaceName | Имя пространства имен |
| IPAddress | IP-адрес клиента, подключающегося к службе концентраторов событий |
| Действие | Действие, выполняемое службой концентраторов событий при оценке запросов на подключение. Поддерживаемые действия **принимают подключение** и **отклоняют подключение**. |
| Причина | Содержит причину, по которой было выполнено действие |
| Счетчик | Количество вхождений для данного действия |
| ResourceId | Идентификатор ресурса Azure Resource Manager. |

### <a name="example"></a>Пример

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Пользовательские журналы с управляемым клиентом ключом
Код JSON пользовательского журнала, управляемого клиентом, включает элементы, перечисленные в следующей таблице.

| Название | Описание |
| ---- | ----------- | 
| Категория | Тип категории для сообщения. Это одно из следующих значений: **Ошибка** и **сведения** . |
| ResourceId | Внутренний идентификатор ресурса, включающий идентификатор подписки Azure и имя пространства имен |
| Хранилище ключей | Имя ресурса Key Vault |
| Key | Имя ключа Key Vault. |
| Версия | Версия ключа Key Vault |
| Операция | Имя операции, выполняемой для обслуживания запросов |
| Код | Код состояния |
| Сообщение | Сообщение, содержащее сведения об ошибке или информационном сообщении |



## <a name="next-steps"></a>Дальнейшие действия
- [Что такое Центры событий Azure?](event-hubs-what-is-event-hubs.md)
- [Общие сведения об API Центров событий](event-hubs-api-overview.md)
- Приступая к работе с Центрами событий
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)

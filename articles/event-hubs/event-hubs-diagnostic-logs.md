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
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509799"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Настройка журналов диагностики для центра событий Azure

Для Центров событий Azure можно просмотреть журналы двух типов.

* **[Журналы действий](../azure-monitor/platform/platform-logs-overview.md)**. Эти журналы содержат сведения об операциях, выполненных над заданием. Данные журналы всегда включены. Чтобы просмотреть записи журнала действий, выберите **Журнал действий** в левой области для пространства имен концентратора событий в портал Azure. Например: "Создание или обновление пространства имен", "Создание или обновление концентратора событий".

    ![Журнал действий для пространства имен концентраторов событий](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Журналы диагностики.](../azure-monitor/platform/platform-logs-overview.md)** Вы можете настроить журналы диагностики, чтобы получать подробные сведения обо всем, что происходит с заданием. Журналы диагностики охватывают действия с момента создания задания до его удаления, включая обновления и действия, которые происходят во время выполнения задания.

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

- **Журналы архивирования.** Журналы, связанные с архивами Центров событий, в частности, с ошибками архива.
- **Операционные журналы.** Сведения о том, что происходит во время операций в Центрах событий, в частности, тип операции, включая создание концентратора событий, используемые ресурсы, а также состояние операции.
- **Автоматическое масштабирование журналов**: сведения об операциях автомасштабирования, выполняемых в пространстве имен концентраторов событий. 
- **Журналы координатора Kafka** — сведения о операциях координатора Kafka, связанных с концентраторами событий. 
- **Журналы пользователей Kafka**: сведения о пользовательских операциях Kafka, связанных с концентраторами событий. 
- **Событие подключения виртуальной сети (VNet) концентраторов событий**: сведения о событиях подключения виртуальной сети концентраторов событий. 
- **Пользовательские журналы управляемого клиентом ключа**: сведения об операциях, связанных с управляемым клиентом ключом. 


    Все журналы хранятся в формате JSON (нотация объектов JavaScript). Каждая запись содержит строковые поля, использующие формат, описанный в следующих разделах.

## <a name="archive-logs-schema"></a>Схема архивных журналов

Строки JSON архивных журналов содержат элементы, перечисленные в следующей таблице.

Имя | Описание
------- | -------
TaskName | Описание задачи, завершившейся сбоем.
ActivityId | Внутренний идентификатор, используемый для отслеживания.
trackingId | Внутренний идентификатор, используемый для отслеживания.
resourceId | Идентификатор ресурса Azure Resource Manager.
eventHub | Полное имя концентратора событий (включает в себя имя пространства имен).
partitionId | Секция концентратора событий, в которую записываются данные.
archiveStep | ArchiveFlushWriter
startTime | Время начала сбоя.
failures | Количество произошедших сбоев.
durationInSeconds | Продолжительность сбоя.
сообщение | Сообщение об ошибке.
категория | ArchiveLogs

Ниже приведен пример строки JSON журнала архивирования.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
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

Имя | Описание
------- | -------
ActivityId | Внутренний идентификатор, используемый для отслеживания.
EventName | Имя операции.  
resourceId | Идентификатор ресурса Azure Resource Manager.
SubscriptionId | Идентификатор подписки.
EventTimeString | Время операции.
EventProperties | Свойства операции.
Состояние | Состояние операции.
Caller | Объект, вызвавший операцию (портал Azure или клиент управления).
категория | OperationalLogs

Ниже приведен пример строки JSON операционного журнала.

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Схема журналов автомасштабирования
Журнал автомасштабирования JSON включает элементы, перечисленные в следующей таблице.

| Имя | Описание |
| ---- | ----------- | 
| trackingId | Внутренний идентификатор, используемый для целей трассировки |
| resourceId | Внутренний идентификатор, содержащий идентификатор подписки Azure и имя пространства имен |
| сообщение | Информационное сообщение, содержащее сведения об автоматическом неструктурированном действии. Сообщение содержит предыдущее и текущее значение единицы пропускной способности для данного пространства имен, а также то, что привело к увеличению TU. |

## <a name="kafka-coordinator-logs-schema"></a>Схема журналов координатора Kafka
JSON журнала координатора Kafka включает элементы, перечисленные в следующей таблице.

| Имя | Описание |
| ---- | ----------- | 
| requestId | Идентификатор запроса, используемый для целей трассировки |
| resourceId | Внутренний идентификатор, содержащий идентификатор подписки Azure и имя пространства имен |
| operationName | Имя операции, выполняемой во время координации групп |
| clientid | ИД клиента |
| namespaceName | Имя пространства имен | 
| subscriptionId | Идентификатор подписки Azure. |
| сообщение | Информационное сообщение, содержащее сведения о действиях, выполненных во время координации групп потребителей. |

## <a name="kafka-user-error-logs-schema"></a>Схема журналов ошибок пользователя Kafka
Журнал ошибок пользователя Kafka. JSON содержит элементы, перечисленные в следующей таблице:

| Имя | Описание |
| ---- | ----------- |
| trackingId | ИДЕНТИФИКАТОР отслеживания, который используется для целей трассировки. |
| namespaceName | Имя пространства имен |
| eventhub | Имя концентратора событий |
| partitionId | Partition ID |
| groupId | Идентификатор группы |
| ClientId | ИД клиента |
| resourceId | Внутренний идентификатор, содержащий идентификатор подписки Azure и имя пространства имен |
| сообщение | Информационное сообщение, содержащее сведения об ошибке |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Схема событий подключения виртуальной сети концентраторов событий

Событие подключения виртуальной сети концентраторов событий (VNet) содержит элементы, перечисленные в следующей таблице:

| Имя | Описание |
| ---  | ----------- | 
| subscriptionId | Идентификатор подписки Azure. |
| namespaceName | Имя пространства имен |
| ipAddress | IP-адрес клиента, подключающегося к службе концентраторов событий |
| action | Действие, выполняемое службой концентраторов событий при оценке запросов на подключение. Поддерживаемые действия: **акцептконнектион** и **режектконнектион**. |
| reason | Содержит причину, по которой было выполнено действие |
| count | Количество вхождений для данного действия |
| resourceId | Внутренний идентификатор ресурса, который содержит идентификатор подписки и имя пространства имен. |

## <a name="customer-managed-key-user-logs"></a>Пользовательские журналы с управляемым клиентом ключом
Код JSON пользовательского журнала, управляемого клиентом, включает элементы, перечисленные в следующей таблице.

| Имя | Описание |
| ---- | ----------- | 
| категория | Тип категории для сообщения. Это одно из следующих значений: **Ошибка** и **сведения** . |
| resourceId | Внутренний идентификатор ресурса, включающий идентификатор подписки Azure и имя пространства имен |
| keyVault | Имя ресурса Key Vault |
| ключ | Имя ключа Key Vault. |
| Версия | Версия ключа Key Vault |
| операции | Имя операции, выполняемой для обслуживания запросов |
| code | Код состояния |
| сообщение | Сообщение, содержащее сведения об ошибке или информационном сообщении |



## <a name="next-steps"></a>Следующие шаги
- [Что такое Центры событий Azure?](event-hubs-what-is-event-hubs.md)
- [Общие сведения об API Центров событий](event-hubs-api-overview.md)
- Приступая к работе с Центрами событий
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)

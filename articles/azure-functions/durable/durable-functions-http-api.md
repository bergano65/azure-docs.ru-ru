---
title: API HTTP в устойчивых функциях — Azure
description: Сведения о том, как внедрять API HTTP в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087265"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API HTTP в устойчивых функциях (Функции Azure)

Расширение устойчивых задач предоставляет набор API HTTP, которые могут использоваться для выполнения следующих задач:

* получение состояния экземпляра оркестрации;
* отправка события в ожидающий экземпляр оркестрации;
* завершение работающего экземпляра оркестрации.

Каждый из этих API HTTP является операцией веб-перехватчика, обрабатываемой напрямую расширением устойчивых задач. Они не относятся к какой-либо функции в приложении-функции.

> [!NOTE]
> Эти операции также могут быть вызваны напрямую с помощью API управления экземплярами в классе [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Обнаружение URL-адреса API HTTP

Класс [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_), который можно использовать для создания полезных данных ответа HTTP, содержащих ссылки на все поддерживаемые операции. Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Эти примеры функций создают следующие данные ответа JSON. Тип данных всех полей — `string`.

| Поле                   |Описание                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |Идентификатор экземпляра оркестрации. |
| **`statusQueryGetUri`**     |URL-адрес состояния экземпляра оркестрации. |
| **`sendEventPostUri`**      |URL-адрес вызова события экземпляра оркестрации. |
| **`terminatePostUri`**      |URL-адрес завершения экземпляра оркестрации. |
| **`purgeHistoryDeleteUri`** |URL-адрес "Очистка журнала" экземпляра оркестрации. |
| **`rewindPostUri`**         |образца URL-адрес "перемотки" экземпляра оркестрации. |

Вот пример ответа на запрос:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Формат URL-адресов веб-перехватчиков может отличаться в зависимости от того, какая версия узла Функций Azure выполняется. Приведенный выше пример использует формат адресов для узла Функций Azure 2.x.

## <a name="async-operation-tracking"></a>Отслеживание асинхронных операций

Упомянутый ранее HTTP-ответ предназначен для помощи в реализации долго выполняющихся асинхронных API-интерфейсов HTTP с устойчивыми функциями. Это иногда называется *шаблоном опрашивающего объекта-получателя*. Поток клиента или сервера работает следующим образом:

1. Клиент отправляет запрос HTTP для запуска длительного процесса, например функции оркестратора.
2. Целевой триггер HTTP возвращает ответ HTTP 202 с заголовком `Location` со значением `statusQueryGetUri`.
3. Клиент отправляет URL-адрес в заголовке `Location`. Он продолжает просматривать ответы HTTP 202 с заголовком `Location`.
4. При завершении (или сбое) экземпляра конечная точка в заголовке `Location` возвращает ответ HTTP 200.

Этот протокол позволяет согласовать долго выполняющиеся процессы с помощью внешних клиентов или служб, которые поддерживают опрос конечной точки HTTP и используют заголовок `Location`. Ключевые компоненты уже встроены в API HTTP устойчивых функций.

> [!NOTE]
> По умолчанию все действия на основе HTTP, предоставляемые [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), поддерживают стандартную модель асинхронных операций. Эта возможность позволяет внедрять долго выполняющиеся устойчивые функции в рамках рабочего процесса Logic Apps. Дополнительные сведения о поддержке Logic Apps для асинхронных шаблонов HTTP см. в разделе [Модель асинхронных операций](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Справочник по API HTTP

Все API HTTP, реализованные с помощью расширения, принимают следующие параметры. Тип данных всех параметров — `string`.

| Параметр        | Тип параметра  | Описание |
|------------------|-----------------|-------------|
| **`taskHub`**    | Строка запроса    | Имя [центра задач](durable-functions-task-hubs.md). Если не указано, предполагается имя центра задач текущего приложения-функции. |
| **`connection`** | Строка запроса    | **Имя** строки подключения для учетной записи хранения. Если не указано, предполагается строка подключения по умолчанию для приложения-функции. |
| **`systemKey`**  | Строка запроса    | Ключ авторизации, необходимый для вызова API. |

`systemKey` — это ключ авторизации, автоматически создаваемый узлом Функций Azure. В частности, он предоставляет доступ к API расширения устойчивых задач и им можно управлять так же, как и [другими ключами авторизации](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Самый простой способ обнаружения значения `systemKey` — с помощью API `CreateCheckStatusResponse`, упомянутого ранее.

В следующих нескольких разделах рассматриваются определенные API HTTP, поддерживаемые расширением, и приведены примеры их использования.

### <a name="get-instance-status"></a>Получение состояния экземпляра

Возвращает состояние определенного экземпляра оркестрации.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле                   | Тип параметра  | Описание |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Идентификатор экземпляра оркестрации. |
| **`showInput`**         | Строка запроса    | Необязательный параметр. Если задано `false`значение, входные данные функции не будут включаться в полезные данные ответа.|
| **`showHistory`**       | Строка запроса    | Необязательный параметр. Если задано значение `true`, журнал выполнения оркестрации будет включен в полезные данные ответа.|
| **`showHistoryOutput`** | Строка запроса    | Необязательный параметр. Если задано `true`значение, выходные данные функции будут включаться в журнал выполнения оркестрации.|
| **`createdTimeFrom`**   | Строка запроса    | Необязательный параметр. При указании фильтрует список возвращаемых экземпляров, созданных в или после заданной метки времени ISO8601.|
| **`createdTimeTo`**     | Строка запроса    | Необязательный параметр. При указании фильтрует список возвращаемых экземпляров, созданных в или перед заданной отметкой времени ISO8601.|
| **`runtimeStatus`**     | Строка запроса    | Необязательный параметр. При указании он фильтрует список возвращаемого значения экземпляров на основе их состояния среды выполнения. Чтобы просмотреть список возможных значений состояния среды выполнения, см. раздел [Запросы экземпляров](durable-functions-instance-management.md). |

#### <a name="response"></a>Отклик

Может быть возвращено несколько кодов состояния.

* **HTTP 200 (OK)** (HTTP 200 (ОК)): указанный экземпляр находится в завершенном состоянии.
* **HTTP 202 (Accepted)** (HTTP 202 (принято)): указанный экземпляр выполняется.
* **HTTP 400 (Bad Request)** (HTTP 400 (недопустимый запрос)): на определенном экземпляре произошел сбой, или его работа была прервана.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): указанный экземпляр не существует или не был запущен.
* **HTTP 500 (Internal Server Error)** (HTTP 500 (внутренняя ошибка сервера)): сбой указанного экземпляра с необработанным исключением.

Полезные данные ответа для случаев **HTTP 200** и **HTTP 202** являются объектами JSON со следующими полями:

| Поле                 | Тип данных | Описание |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | строка    | Состояние среды выполнения экземпляра. Возможные значения: *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed*. |
| **`input`**           | JSON      | Данные JSON, используемые для инициализации экземпляра. Это поле имеет значение `null`, если для параметра строки запроса `showInput` задано значение `false`.|
| **`customStatus`**    | JSON      | Данные JSON, используемые для состояния пользовательской оркестрации. Если поле не заполнено, для него устанавливается значение `null`. |
| **`output`**          | JSON      | Выходные данные JSON экземпляра. Это поле имеет значение `null`, если экземпляр не находится в завершенном состоянии. |
| **`createdTime`**     | строка    | Время, когда был создан экземпляр. Использует расширенную нотацию ISO 8601. |
| **`lastUpdatedTime`** | строка    | Время, когда экземпляр был в последний раз сохранен. Использует расширенную нотацию ISO 8601. |
| **`historyEvents`**   | JSON      | Массив JSON, содержащий журнал выполнения оркестрации. Это поле имеет значение `null`, если для параметра строки запроса `showHistory` не задано значение `true`. |

Ниже приведен пример полезных данных ответа, включающий журнал выполнения оркестрации и выходные данные действия (в удобном для чтения формате).

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Ответ **HTTP 202** также включает заголовок ответа **Location**, который ссылается на тот же URL-адрес, что и поле `statusQueryGetUri`, упомянутое ранее.

### <a name="get-all-instances-status"></a>Получение состояния всех экземпляров

Можно также запросить состояние всех экземпляров, удалив `instanceId` из запроса "получение состояния экземпляра". В этом случае основные параметры аналогичны параметру "получить состояние экземпляра". Также поддерживаются параметры строки запроса для фильтрации.

Следует помнить, что `connection` и `code` являются дополнительными. Если у вас есть анонимная проверка подлинности на функцию, кода не требуется.
Если вы не хотите использовать другую строку подключения к хранилищу, не определенную в параметре приложения AzureWebJobsStorage, можно спокойно проигнорировать параметр строки запроса подключения.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле                   | Тип параметра  | Описание |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Идентификатор экземпляра оркестрации. |
| **`showInput`**         | Строка запроса    | Необязательный параметр. Если задано `false`значение, входные данные функции не будут включаться в полезные данные ответа.|
| **`showHistory`**       | Строка запроса    | Необязательный параметр. Если задано значение `true`, журнал выполнения оркестрации будет включен в полезные данные ответа.|
| **`showHistoryOutput`** | Строка запроса    | Необязательный параметр. Если задано `true`значение, выходные данные функции будут включаться в журнал выполнения оркестрации.|
| **`createdTimeFrom`**   | Строка запроса    | Необязательный параметр. При указании фильтрует список возвращаемых экземпляров, созданных в или после заданной метки времени ISO8601.|
| **`createdTimeTo`**     | Строка запроса    | Необязательный параметр. При указании фильтрует список возвращаемых экземпляров, созданных в или перед заданной отметкой времени ISO8601.|
| **`runtimeStatus`**     | Строка запроса    | Необязательный параметр. При указании он фильтрует список возвращаемого значения экземпляров на основе их состояния среды выполнения. Чтобы просмотреть список возможных значений состояния среды выполнения, см. раздел [Запросы экземпляров](durable-functions-instance-management.md). |
| **`top`**               | Строка запроса    | Необязательный параметр. Если указано, ограничивает количество экземпляров, возвращаемых запросом. |

#### <a name="response"></a>Отклик

Вот пример полезных данных ответа, включая состояние оркестрации (в удобном для чтения формате):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Эта операция может быть весьма затратной с точки зрения операций ввода-вывода службы хранилища Azure, если в таблице экземпляров много строк. Дополнительные сведения о таблице экземпляров см. в документации по [производительности и масштабируемости в устойчивых функциях (Функциях Azure)](durable-functions-perf-and-scale.md#instances-table).
>

Если имеются дополнительные результаты, в заголовке ответа возвращается маркер продолжения.  Имя заголовка — `x-ms-continuation-token`.

Если задать значение маркера продолжения в заголовке следующего запроса, можно получить следующую страницу результатов. Это имя заголовка запроса также `x-ms-continuation-token`является.

### <a name="purge-single-instance-history"></a>Очистить журнал одного экземпляра

Удаляет журнал и связанные с ним артефакты для указанного экземпляра оркестрации.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле             | Тип параметра  | Описание |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Идентификатор экземпляра оркестрации. |

#### <a name="response"></a>Отклик

Могут возвращаться следующие значения кода состояния HTTP.

* **HTTP 200 (OK)** (HTTP 200 (ОК)): Журнал экземпляров успешно удален.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): Указанный экземпляр не существует.

Полезные данные ответа для варианта **HTTP 200** — это объект JSON со следующим полем:

| Поле                  | Тип данных | Описание |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | целочисленный   | Число удаленных экземпляров. В случае с одним экземпляром это значение всегда должно быть `1`равно. |

Ниже приведен пример полезных данных ответа (в формате, удобном для чтения):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Очистка журнала нескольких экземпляров

Кроме того, можно удалить журнал и связанные с ним артефакты для нескольких экземпляров в центре задач, `{instanceId}` удалив из запроса "Очистка журнала единственных экземпляров". Чтобы выборочно очистить журнал экземпляров, используйте те же фильтры, которые описаны в запросе "получение состояния всех экземпляров".

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле                 | Тип параметра  | Описание |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Строка запроса    | Фильтрует список удаленных экземпляров, созданных в или после указанной метки времени ISO8601.|
| **`createdTimeTo`**   | Строка запроса    | Необязательный параметр. При указании фильтрует список удаленных экземпляров, созданных в или до указанной метки времени ISO8601.|
| **`runtimeStatus`**   | Строка запроса    | Необязательный параметр. Если этот параметр указан, список удаленных экземпляров фильтруется в зависимости от их состояния выполнения. Чтобы просмотреть список возможных значений состояния среды выполнения, см. раздел [Запросы экземпляров](durable-functions-instance-management.md). |

> [!NOTE]
> Эта операция может быть очень дорогостоящей в плане операций ввода-вывода службы хранилища Azure при наличии большого количества строк в таблицах экземпляров и журнала. Дополнительные сведения об этих таблицах можно найти в документации по [производительности и масштабированию в устойчивые функции (функции Azure)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Отклик

Могут возвращаться следующие значения кода состояния HTTP.

* **HTTP 200 (OK)** (HTTP 200 (ОК)): Журнал экземпляров успешно удален.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): Не найдены экземпляры, соответствующие критерию фильтра.

Полезные данные ответа для варианта **HTTP 200** — это объект JSON со следующим полем:

| Поле                   | Тип данных | Описание |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | целочисленный   | Число удаленных экземпляров. |

Ниже приведен пример полезных данных ответа (в формате, удобном для чтения):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Вызов события

Отправляет сообщение уведомления о событии в выполняющийся экземпляр оркестрации.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле             | Тип параметра  | Описание |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Идентификатор экземпляра оркестрации. |
| **`eventName`**   | URL             | Имя события, которое ожидает целевой экземпляр оркестрации. |
| **`{content}`**   | Содержимое запроса | Полезные данные события в формате JSON. |

#### <a name="response"></a>Отклик

Может быть возвращено несколько кодов состояния.

* **HTTP 202 (Accepted)** (HTTP 202 (принято)): сгенерированное событие принято в обработку.
* **HTTP 400 (Bad Request)** (HTTP 400 (недопустимый запрос)): содержимое запроса не является допустимым типом `application/json` или не соответствует формату JSON.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): указанный экземпляр не найден.
* **HTTP 410 (Gone)** (HTTP 410 (потеряно)): указанный экземпляр выполнен или завершился с ошибкой и не может обрабатывать возникающие события.

Ниже приведен пример запроса, отправляющий строку JSON `"incr"` в экземпляр, который ожидает событие с именем **operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Ответы этого API не содержат какого-либо содержимого.

### <a name="terminate-instance"></a>Завершение экземпляра

Завершите выполнение экземпляра оркестрации.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующий уникальный параметр.

| Поле             | Тип параметра  | Описание |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Идентификатор экземпляра оркестрации. |
| **`reason`**      | Строка запроса    | Необязательный элемент. Причина завершения работы экземпляра оркестрации. |

#### <a name="response"></a>Отклик

Может быть возвращено несколько кодов состояния.

* **HTTP 202 (Accepted)** (HTTP 202 (принято)): запрос на завершение принят в обработку.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): указанный экземпляр не найден.
* **HTTP 410 (Gone)** (HTTP 410 (потеряно)): определенный экземпляр выполнен успешно или завершился сбоем.

Ниже приведен пример запроса, который завершает выполнение экземпляра и указывает причину **ошибки**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Ответы этого API не содержат какого-либо содержимого.

### <a name="rewind-instance-preview"></a>Экземпляр перемотки (предварительная версия)

Восстанавливает сбойный экземпляр оркестрации в рабочем состоянии путем воспроизведения последних неудачных операций.

#### <a name="request"></a>Запрос

Для версии 1. x среды выполнения функций запрос форматируется следующим образом (для ясности отображается несколько строк):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

В версии 2. x среды выполнения функций формат URL-адреса имеет те же параметры, но с немного отличающимся префиксом:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующий уникальный параметр.

| Поле             | Тип параметра  | Описание |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Идентификатор экземпляра оркестрации. |
| **`reason`**      | Строка запроса    | Необязательный элемент. Причина перемотки экземпляра оркестрации. |

#### <a name="response"></a>Отклик

Может быть возвращено несколько кодов состояния.

* **HTTP 202 (Accepted)** (HTTP 202 (принято)): запрос на перемотку принят в обработку.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): указанный экземпляр не найден.
* **HTTP 410 (Gone)** (HTTP 410 (потеряно)): определенный экземпляр выполнен успешно или был прерван.

Ниже приведен пример запроса, который перематывает сбойный экземпляр и указывает причину **исправления**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Ответы этого API не содержат какого-либо содержимого.

### <a name="signal-entity-preview"></a>Объект Signal (Предварительная версия)

Отправляет односторонняя сообщение операции в [устойчивую сущность](durable-functions-types-features-overview.md#entity-functions). Если сущность не существует, она будет создана автоматически.

#### <a name="request"></a>Запрос

HTTP-запрос форматируется следующим образом (для ясности показаны несколько строк):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле             | Тип параметра  | Описание |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Тип сущности. |
| **`entityKey`**   | URL             | Уникальное имя сущности. |
| **`op`**          | Строка запроса    | Необязательный элемент. Имя вызываемой определяемой пользователем операции. |
| **`{content}`**   | Содержимое запроса | Полезные данные события в формате JSON. |

Ниже приведен пример запроса, который отправляет определяемое пользователем сообщение `Counter` Add в сущность с именем. `steps` Содержимое сообщения — это значение `5`. Если сущность еще не существует, она будет создана этим запросом:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Отклик

Эта операция имеет несколько возможных ответов:

* **HTTP 202 (Accepted)** (HTTP 202 (принято)): Операция сигнала была принята для асинхронной обработки.
* **HTTP 400 (Bad Request)** (HTTP 400 (недопустимый запрос)): Содержимое запроса не было типа `application/json`, не является допустимым JSON или имело недопустимое `entityKey` значение.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): Указанный `entityType` объект не найден.

Успешный HTTP-запрос не содержит никакого содержимого в ответе. Неудачный HTTP-запрос может содержать сведения об ошибке в формате JSON в содержимом ответа.

### <a name="query-entity-preview"></a>Сущность запроса (Предварительная версия)

Возвращает состояние указанной сущности.

#### <a name="request"></a>Запрос

HTTP-запрос форматируется следующим образом (для ясности показаны несколько строк):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Отклик

Эта операция имеет два возможных ответа:

* **HTTP 200 (OK)** (HTTP 200 (ОК)): Указанная сущность существует.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)): Указанная сущность не найдена.

Успешный ответ содержит сериализованное в JSON состояние сущности в качестве ее содержимого.

#### <a name="example"></a>Пример
Ниже приведен пример HTTP-запроса, который получает состояние существующей `Counter` сущности с именем: `steps`

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Если сущность просто содержала несколько действий, сохраненных `currentValue` в поле, содержимое ответа может выглядеть следующим образом (отформатировано для удобочитаемости): `Counter`

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Обработка ошибок в устойчивых функциях (Функции Azure)](durable-functions-error-handling.md)

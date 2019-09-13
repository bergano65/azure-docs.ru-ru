---
title: Функции HTTP в Устойчивые функции — функции Azure
description: Сведения об интегрированных функциях HTTP в расширении Устойчивые функции для функций Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935798"
---
# <a name="http-features"></a>Функции HTTP

Устойчивые функции имеет несколько функций, которые упрощают внедрение устойчивых оркестрации и сущностей в рабочие процессы HTTP. В этой статье приводятся подробные сведения о некоторых из этих функций.

## <a name="exposing-http-apis"></a>Предоставление API-интерфейсов HTTP

Оркестрации и сущности могут вызываться и управляться с помощью HTTP-запросов. Расширение Устойчивые функции предоставляет встроенные API HTTP, а также предоставляет интерфейсы API для взаимодействия с оркестрации и сущностями из функций, активируемых HTTP.

### <a name="built-in-http-apis"></a>Встроенные API HTTP

Расширение Устойчивые функции автоматически добавляет набор API-интерфейсов HTTP к узлу функций Azure. Эти интерфейсы API позволяют взаимодействовать и управлять согласованиями и сущностями без написания кода.

Поддерживаются следующие встроенные API HTTP.

* [Начать новое согласование](durable-functions-http-api.md#start-orchestration)
* [Экземпляр оркестрации запросов](durable-functions-http-api.md#get-instance-status)
* [Завершить экземпляр оркестрации](durable-functions-http-api.md#terminate-instance)
* [Отправка внешнего события в оркестрации](durable-functions-http-api.md#raise-event)
* [Очистка журнала оркестрации](durable-functions-http-api.md#purge-single-instance-history)
* [Отправка события операции в сущность](durable-functions-http-api.md#signal-entity)
* [Запрос состояния сущности](durable-functions-http-api.md#query-entity)

Полное описание всех встроенных API HTTP, предоставляемых расширением Устойчивые функции, см. в статье [API-интерфейсы HTTP](durable-functions-http-api.md) .

### <a name="http-api-url-discovery"></a>Обнаружение URL-адреса API HTTP

[Привязка клиента оркестрации](durable-functions-bindings.md#orchestration-client) предоставляет интерфейсы API, которые можно использовать для создания удобных полезных данных ответа HTTP. Например, он может создать ответ, содержащий ссылки на API управления для конкретного экземпляра оркестрации. Ниже приведен пример функции HTTP-триггера, демонстрирующий использование этого API для нового экземпляра оркестрации:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>Скрипт C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Запуск функции Orchestrator с использованием показанных выше функций триггера HTTP можно выполнить с помощью любого HTTP-клиента. Следующая фигурная команда запускает функцию Orchestrator с именем `DoWork`.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Ниже приведен пример ответа для оркестрации с `abc123` идентификатором (некоторые сведения удалены для ясности):

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Каждое из `~Uri` полей в предыдущем примере соответствует встроенным API-интерфейсам HTTP. Эти API-интерфейсы можно использовать для управления целевым экземпляром оркестрации.

> [!NOTE]
> Формат URL-адресов веб-перехватчиков может отличаться в зависимости от того, какая версия узла Функций Azure выполняется. Приведенный выше пример предназначен для узла Функций Azure 2.0.

Описание всех встроенных API HTTP см. в справочной документации по [HTTP API](durable-functions-http-api.md) .

### <a name="async-operation-tracking"></a>Отслеживание асинхронных операций

Упомянутый ранее HTTP-ответ предназначен для помощи в реализации долго выполняющихся асинхронных API-интерфейсов HTTP с устойчивыми функциями. Этот шаблон иногда называют *шаблоном объекта-получателя опроса*. Поток клиента или сервера работает следующим образом:

1. Клиент отправляет запрос HTTP для запуска длительного процесса, например функции оркестратора.
2. Целевой триггер HTTP возвращает ответ HTTP 202 с заголовком `Location` со значением `statusQueryGetUri`.
3. Клиент отправляет URL-адрес в заголовке `Location`. Он продолжает просматривать ответы HTTP 202 с заголовком `Location`.
4. При завершении (или сбое) экземпляра конечная точка в заголовке `Location` возвращает ответ HTTP 200.

Этот протокол позволяет согласовать долго выполняющиеся процессы с помощью внешних клиентов или служб, которые поддерживают опрос конечной точки HTTP и используют заголовок `Location`. Серверные и клиентские реализации этого шаблона встроены в API-интерфейсы Устойчивые функции HTTP.

> [!NOTE]
> По умолчанию все действия на основе HTTP, предоставляемые [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), поддерживают стандартную модель асинхронных операций. Эта возможность позволяет внедрять долго выполняющиеся устойчивые функции в рамках рабочего процесса Logic Apps. Дополнительные сведения о поддержке Logic Apps для асинхронных шаблонов HTTP см. в разделе [Модель асинхронных операций](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Взаимодействие с оркестрации может осуществляться из любого типа функции, а не только с помощью функций, активируемых HTTP.

Дополнительные сведения об управлении согласованиями и сущностями с помощью клиентских API см. в статье [Управление экземплярами](durable-functions-instance-management.md) .

## <a name="consuming-http-apis"></a>Использование API-интерфейсов HTTP

Функции Orchestrator не могут выполнять операции ввода-вывода напрямую, как описано в разделе [ограничения кода функции Orchestrator](durable-functions-code-constraints.md). Вместо этого функции Orchestrator обычно вызывают [функции действий](durable-functions-types-features-overview.md#activity-functions) , выполняющие операции ввода-вывода.

Начиная с Устойчивые функции 2,0, оркестрации могут использовать API-интерфейсы HTTP, используя [привязку триггера оркестрации](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> Возможность вызова конечных точек HTTP непосредственно из функций Orchestrator пока недоступна в JavaScript.

В следующем примере кода показана C# функция Orchestrator, выполняющая исходящий HTTP- `CallHttpAsync` запрос с помощью API .NET:

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

Действие "вызов HTTP" позволяет выполнять следующие действия в функциях Orchestrator:

* Вызывайте API HTTP непосредственно из функций оркестрации (с некоторыми ограничениями, упомянутыми ниже).
* Автоматическая поддержка шаблонов опроса состояния HTTP 202 на стороне клиента.
* Используйте [управляемые удостоверения Azure](../../active-directory/managed-identities-azure-resources/overview.md) для выполнения санкционированных вызовов HTTP к другим конечным точкам Azure.

Возможность использования API-интерфейсов HTTP непосредственно из функций Orchestrator предназначена для удобства работы с определенным набором распространенных сценариев. Все эти функции можно реализовать самостоятельно с помощью функций действий. Во многих случаях функции действий могут обеспечить большую гибкость.

### <a name="http-202-handling"></a>Обработка HTTP 202

API "Call HTTP" может автоматически реализовать клиентскую сторону *шаблона опрашивающего потребителя*. Если вызываемый API возвращает ответ HTTP 202 с `Location` заголовком, функция Orchestrator автоматически `Location` опрашивает ресурс до тех пор, пока не будет возвращен ответ, не относящийся к 202. Ответ, отличный от 202, будет ответом, возвращенным в код функции Orchestrator.

> [!NOTE]
> Функции Orchestrator также изначально поддерживают *клиентский шаблон опроса*на стороне сервера, как описано в разделе [Отслеживание асинхронных операций](#async-operation-tracking). Это означает, что согласование в одном приложении функции может легко координировать функции Orchestrator в других приложениях функций. Это похоже на концепцию [подсистемы оркестрации](durable-functions-sub-orchestrations.md) , но с поддержкой взаимодействия между приложениями. Это особенно полезно для разработки приложений в стиле микрослужб.

### <a name="managed-identities"></a>Управляемые удостоверения

Устойчивые функции изначально поддерживает вызов API-интерфейсов, которые принимают маркеры Azure AD для авторизации. Эта поддержка использует [управляемые удостоверения Azure](../../active-directory/managed-identities-azure-resources/overview.md) для получения этих маркеров.

Следующий код является примером функции .NET Orchestrator, которая выполняет проверку подлинности вызовов для перезапуска виртуальной машины с помощью Azure Resource Manager [виртуальных машин REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

В предыдущем примере `tokenSource` параметр настроен для получения маркеров Azure AD для [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (идентифицируемого "URI ресурса" `https://management.core.windows.net`). В этом примере предполагается, что текущее приложение-функция либо выполняется локально, либо было развернуто как приложение функций Azure с управляемым удостоверением. Предполагается, что локальным удостоверением или управляемым удостоверением предоставлено разрешение на управление виртуальными машинами в указанной группе `myRG`ресурсов.

Во время выполнения настроенный источник токена автоматически возвращает маркер доступа OAuth 2,0 и добавляет его в качестве токена носителя в `Authorization` заголовок исходящего запроса. Эта модель является улучшением по сравнению с добавлением заголовков авторизации вручную в HTTP-запросы по следующим причинам:

* Обновление токена обрабатывается автоматически. Вам не нужно беспокоиться о токенах с истекшим сроком действия.
* Токены никогда не хранятся в состоянии устойчивого оркестрации.
* Вам не нужно писать какой бы то ни было код, чтобы справиться с получением маркера.

Более полный пример можно найти в [предварительно скомпилированных примерах C# "рестартвмс"](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Управляемые удостоверения не ограничиваются управлением ресурсами Azure. Управляемые удостоверения можно использовать для доступа к любому API, который принимает токены носителя Azure AD, включая основные службы Azure или веб-приложения сторонних производителей. Веб-приложение стороннего производителя может даже быть другим приложением-функцией. Список служб Azure, которые поддерживают проверку подлинности в Azure AD, см. в статье [службы Azure, поддерживающие аутентификацию Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Ограничения

Встроенная поддержка вызова API-интерфейсов HTTP является удобной функцией и не подходит для всех сценариев. HTTP-запросы, отправленные функциями Orchestrator и их ответами, сериализуются и сохраняются как сообщения очереди. Такое поведение очередей гарантирует надежность и безопасность HTTP-вызовов при [воспроизведении оркестрации](durable-functions-orchestrations.md#reliability). Однако такое поведение очереди также означает, что:

* Каждый HTTP-запрос требует дополнительной задержки по сравнению с собственным HTTP-клиентом.
* Большие сообщения запроса или ответа, которые не могут поместиться в очередь, могут значительно снизить производительность оркестрации. Потенциальное снижение производительности связано с нагрузкой полезных данных сообщения в хранилище BLOB-объектов.
* Потоковая передача, фрагментированность и двоичные данные не поддерживаются.
* Возможность настройки поведения клиента HTTP ограничена.

Если какое-либо из этих ограничений может повлиять на вариант использования, вместо этого следует использовать функции действий и клиентские библиотеки HTTP, зависящие от языка, для выполнения исходящих HTTP-вызовов.

> [!NOTE]
> Если вы являетесь разработчиком .NET, возможно, вас интересует, почему эта функция `DurableHttpRequest` использует `DurableHttpResponse` и типы вместо встроенных .NET `HttpRequestMessage` и `HttpResponseMessage`. Этот вариант разработки был намеренным. Основная причина заключается в том, что пользовательские типы помогают убедиться в том, что пользователи не делают неправильные предположения о поддерживаемых поведениях внутреннего HTTP-клиента. Устойчивые типы также позволяют упростить разработку API и сделать более простыми специальные функции, такие как [Интеграция управляемой идентификации](#managed-identities) и [шаблон опрашивающего потребителя](#http-202-handling).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Сведения о устойчивых сущностях](durable-functions-entities.md)
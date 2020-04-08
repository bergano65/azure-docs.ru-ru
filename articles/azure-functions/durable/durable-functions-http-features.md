---
title: Функции HTTP в функции долговечных - Функции Azure
description: Узнайте о интегрированных функциях HTTP в расширении долгосрочных функций для функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802389"
---
# <a name="http-features"></a>Особенности HTTP

Долгосрочные функции имеет несколько функций, которые делают его легко включить прочные оркестровки и сущности в рабочие процессы HTTP. Эта статья идет в детали о некоторых из тех характеристик.

## <a name="exposing-http-apis"></a>Разоблачение HTTP APIs

Оркестровки и сущности могут вызываться и управляться с помощью запросов HTTP. Расширение Durable Functions предоставляет встроенные HTTP AIS. Он также предоставляет AAP для взаимодействия с оркестровками и сущностями из функций HTTP-триггера.

### <a name="built-in-http-apis"></a>Встроенные HTTP AIS

Расширение «Долговечные функции» автоматически добавляет набор APИ HTTP в хост Azure Functions. С помощью этих AAP вы можете взаимодействовать с оркестровками и сущностями и управлять ими, не записывая код.

Поддерживаются следующие встроенные AIS HTTP.

* [Начать новую оркестровку](durable-functions-http-api.md#start-orchestration)
* [Пример оркестровки запросов](durable-functions-http-api.md#get-instance-status)
* [Прекращение экземпляра оркестровки](durable-functions-http-api.md#terminate-instance)
* [Отправить внешнее событие в оркестровку](durable-functions-http-api.md#raise-event)
* [История очистки оркестровки](durable-functions-http-api.md#purge-single-instance-history)
* [Отправка события операции объекту](durable-functions-http-api.md#signal-entity)
* [Получить состояние сущности](durable-functions-http-api.md#get-entity)
* [Запрос списка сущностей](durable-functions-http-api.md#list-entities)

Смотрите [статью HTTP AIS](durable-functions-http-api.md) для полного описания всех встроенных AA HTTP, подверженных расширению долгосрочных функций.

### <a name="http-api-url-discovery"></a>Обнаружение URL-адреса API HTTP

[Связывание клиента-оркестра](durable-functions-bindings.md#orchestration-client) предоставляет AIS, которые могут генерировать удобные полезные нагрузки http-ответов. Например, он может создать ответ, содержащий ссылки на Управечные AIS для конкретного экземпляра оркестровки. Следующие примеры показывают функцию HTTP-trigger, которая демонстрирует, как использовать этот API для нового экземпляра оркестровки:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**Файл index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Запуск функции оркестратора с помощью показанных ранее функций HTTP-триггера может быть выполнен с помощью любого клиента HTTP. Следующая команда cURL запускает функцию `DoWork`оркестратора под названием:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Далее приводится пример ответа для `abc123` оркестровки, которая имеет в качестве идентификатора. Некоторые детали были удалены для ясности.

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

В предыдущем примере каждое из `Uri` полей, заканчивающихся в соответствии со встроенным HTTP API. Эти AAP можно использовать для управления экземпляром целевой оркестровки.

> [!NOTE]
> Формат URL-адресов webhook зависит от того, какую версию хоста Azure Functions вы выполняете. Предыдущий пример — для хоста Azure Functions 2.0.

Для описания всех встроенных API HTTP [см.](durable-functions-http-api.md)

### <a name="async-operation-tracking"></a>Отслеживание асинхронных операций

Упомянутый ранее HTTP-ответ предназначен для помощи в реализации долго выполняющихся асинхронных API-интерфейсов HTTP с устойчивыми функциями. Эта модель иногда упоминается как *шаблон опроса потребителей.* Поток клиента или сервера работает следующим образом:

1. Клиент выдает запрос HTTP, чтобы начать длительный процесс, как функция оркестратора.
1. Целевой триггер HTTP возвращает ответ HTTP 202 с заголовком местоположения, который имеет значение "статусКуэригетури".
1. Клиент осваивай URL в заголовке "Местоположение". Клиент продолжает видеть ответы HTTP 202 с заголовком местоположения.
1. Когда экземпляр завершается или завершается, конечная точка заголовка —сявра возвращает сяритоге HTTP 200.

Этот протокол позволяет координировать длительные процессы с внешними клиентами или службами, которые могут опросить конечную точку HTTP и следовать заголовку местоположения. Как клиент, так и сервер реализации этого шаблона встроены в долгосрочные функции HTTP AIS.

> [!NOTE]
> По умолчанию все действия на основе HTTP, предоставляемые [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), поддерживают стандартную модель асинхронных операций. Эта возможность позволяет внедрять долго выполняющиеся устойчивые функции в рамках рабочего процесса Logic Apps. Более подробную информацию можно найти в поддержке Logic Apps для асинхронных шаблонов HTTP в [действиях рабочего процесса Azure Logic Apps и триггеров документации.](../../logic-apps/logic-apps-workflow-actions-triggers.md)

> [!NOTE]
> Взаимодействия с оркестровками могут осуществляться с любого типа функции, а не только с помощью функций HTTP-триггера.

Для получения дополнительной информации о том, как управлять оркестровками и сущностями, использующими AI-аДИ клиентов, см. [Instance management article](durable-functions-instance-management.md)

## <a name="consuming-http-apis"></a>Потребление HTTP APIs

Как описано в [ограничениях кода функции оркестратора,](durable-functions-code-constraints.md)функции оркестратора не могут делать ввог/о напрямую. Вместо этого они обычно вызывают [функции активности,](durable-functions-types-features-overview.md#activity-functions) которые делают операции ввоза/о.

Начиная с функций durable 2.0, оркестровки могут использовать HTTP AIS с помощью [связывания триггера оркестровки.](durable-functions-bindings.md#orchestration-trigger)

Следующий пример кода показывает функцию оркестратора, выполняя исходящий запрос HTTP:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Используя действие "вызов HTTP", вы можете выполнять следующие действия в функциях оркестратора:

* Вызов http AIS непосредственно из функций оркестровки, с некоторыми ограничениями, которые упоминаются позже.
* Автоматики поддерживать шаблоны опроса статуса клиента http 202.
* Используйте [управляемые идентификаторы Azure](../../active-directory/managed-identities-azure-resources/overview.md) для вызова авторизованных вызовов HTTP в другие конечные точки Azure.

Возможность потреблять HTTP AIS непосредственно из функций оркестратора предназначена для удобства для определенного набора общих сценариев. Вы можете реализовать все эти функции самостоятельно, используя функции активности. Во многих случаях функции активности могут дать вам больше гибкости.

### <a name="http-202-handling"></a>ОБРАБОТКа HTTP 202

API "вызов HTTP" может автоматически реализовать клиентскую сторону потребительского шаблона опроса. Если вызванный API возвращает ответ HTTP 202 заголовком Location, функция оркестратора автоматически осследует ресурс «Местоположение» до получения ответа, за исключением 202. Этот ответ будет ответом, возвращенным в код функции оркестратора.

> [!NOTE]
> Функции Оркестратора также намеренно поддерживают шаблон опроса на стороне сервера, как описано в [отслеживании операций Async.](#async-operation-tracking) Эта поддержка означает, что оркестровки в одном приложении функции могут легко координировать функции оркестратора в других функциональных приложениях. Это похоже на концепцию [суб-оркестрации,](durable-functions-sub-orchestrations.md) но при поддержке кросс-приложения связи. Эта поддержка особенно полезна для разработки приложений в стиле микрослужб.

### <a name="managed-identities"></a>Управляемые удостоверения

Компания Durable Functions намеренно поддерживает вызовы AIS, которые принимают маркеры Active Directory Azure (Azure AD) для авторизации. Эта поддержка использует [управляемые идентификаторы Azure](../../active-directory/managed-identities-azure-resources/overview.md) для приобретения этих токенов.

Следующий код является примером функции оркестратора .NET. Функция делает аутентифицированные вызовы для перезагрузки виртуальной машины с помощью виртуальных машин Azure Resource Manager [REST API.](https://docs.microsoft.com/rest/api/compute/virtualmachines)

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

В предыдущем примере `tokenSource` параметр настроен для приобретения токенов Azure AD для [менеджера ресурсов Azure.](../../azure-resource-manager/management/overview.md) Токены идентифицируются ресурсом `https://management.core.windows.net`URI. Пример предполагает, что текущее приложение функции либо работает локально, либо было развернуто как функциональное приложение с управляемым интилитатом. Предполагается, что локальная идентификация или управляемая идентификация `myRG`имеют разрешение на управление ввосажными данными в указанной группе ресурсов.

Во время выполнения настроенный источник токенов автоматически возвращает токен доступа OAuth 2.0. Затем источник добавляет маркер в качестве маркера носителя в заголовок авторизации исходящего запроса. Эта модель является улучшением по сравнению с ручным добавлением заголовков авторизаций в запросы HTTP по следующим причинам:

* Обновление маркеров обрабатывается автоматически. Вам не нужно беспокоиться о просроченных токенах.
* Токены никогда не хранятся в состоянии прочной оркестровки.
* Вам не нужно писать код для управления приобретением токенов.

Более полный пример можно найти в [предварительно собранном образце RestartVMs.](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)

Управляемые идентификаторы не ограничиваются управлением ресурсами Azure. Управляемые идентификаторы можно использовать для доступа к любому API, который принимает токены на предъявителя Azure AD, включая службы Azure от Microsoft и веб-приложения от партнеров. Веб-приложение партнера может быть даже еще одним приложением функции. Для списка служб Azure от корпорации Майкрософт, поддерживающих аутентификацию с помощью Azure AD, см. [службы Azure, поддерживающие аутентификацию Azure AD.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

### <a name="limitations"></a>Ограничения

Встроенная поддержка вызова HTTP AIS является функцией удобства. Это не подходит для всех сценариев.

Запросы HTTP, отправляемые функциями оркестратора, и их ответы сериализуются и настойчивы в виде сообщений очередей. Такое поведение очередей гарантирует, что вызовы HTTP [являются надежными и безопасными для воспроизведения оркестровки.](durable-functions-orchestrations.md#reliability) Тем не менее, поведение очередей также имеет ограничения:

* Каждый запрос HTTP предполагает дополнительную задержку по сравнению с родным клиентом HTTP.
* Большие сообщения запроса или ответа, которые не могут поместиться в сообщение очереди, могут значительно ухудшить производительность оркестровки. Накладные расходы на разгрузку полезной нагрузки сообщений для хранения капли могут привести к потенциальной деградации производительности.
* Потоковые, шустые и двоичные полезные нагрузки не поддерживаются.
* Возможность настройки поведения клиента HTTP ограничена.

Если какое-либо из этих ограничений может повлиять на случай использования, вместо этого подумайте об использовании функций активности и клиентских библиотек HTTP для исходящих вызовов HTTP.

> [!NOTE]
> Если вы являетесь разработчиком .NET, вы можете задаться вопросом, почему эта функция использует **durableHttpRequest** и **DurableHttpResponse** типы вместо встроенных .NET **HttpRequestMessage** и **HttpResponseMessage** типов.
>
> Такое поведение реализовано намеренно. Основная причина заключается в том, что пользовательские типы помогают гарантировать, что пользователи не делают неверных предположений о поддерживаемом поведении внутреннего клиента HTTP. Типы, специфичных для прочных функций, также позволяют упростить дизайн API. Они также могут более легко сделать доступными специальные функции, такие как [управляемая интеграция идентичности](#managed-identities) и [шаблон опроса потребителей.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Расширяемость (только.NET)

Настройка поведения внутреннего клиента HTTP-функции организации возможна с помощью [инъекций зависимости Azure .NET.](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) Эта способность может быть полезна для небольших поведенческих изменений. Он также может быть полезен для модульного тестирования клиента HTTP путем введения макетных объектов.

Следующий пример демонстрирует использование инъекции зависимости для отсрочения проверки сертификата TLS/SSL для функций оркестратора, которые вызывают внешние конечные точки HTTP.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте о долговременных объектах](durable-functions-entities.md)

---
title: Веб-API, вызывающий веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает веб-API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b1582af2bbd97579852ead0d4462f80f3a50fe6a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257152"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Веб-API, вызывающий веб-API: вызов API

После создания маркера можно вызвать защищенный веб-API. Обычно подчиненные API вызываются из контроллера или страниц веб-API.

## <a name="controller-code"></a>Код контроллера

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

При использовании *Microsoft. Identity. Web*у вас есть три сценария использования:

- [Вызов Microsoft Graph](#call-microsoft-graph)
- [Вызов веб-API, отличного от Microsoft Graph](#call-web-api-other-than-microsoft-graph)
- [Получение маркера вручную](#acquire-a-token-manually)

#### <a name="call-microsoft-graph"></a>Вызов Microsoft Graph

В этом сценарии вы добавили `.AddMicrosoftGraph()` в *Startup.CS* , как указано в [конфигурации кода](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), и можете напрямую внедрить `GraphServiceClient` в контроллер или конструктор страницы для использования в действиях. В следующем примере на странице Razor отображается фотография пользователя, выполнившего вход.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="call-web-api-other-than-microsoft-graph"></a>Вызов веб-API, отличного от Microsoft Graph

В этом сценарии вы добавили `.AddDownstreamWebApi()` в *Startup.CS* , как указано в [конфигурации кода](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), и можете напрямую внедрить `IDownstreamWebApi` службу в контроллер или конструктор страниц и использовать ее в действиях:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

`CallWebApiForUserAsync`Метод также имеет строго типизированные универсальные переопределения, позволяющие напрямую получить объект. Например, следующий метод получил `Todo` экземпляр, который является строго типизированным представлением JSON, возвращенного веб-API.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="acquire-a-token-manually"></a>Получение маркера вручную

Если вы решили получить маркер вручную с помощью `ITokenAcquisition` службы, теперь необходимо использовать токен. В этом случае следующий код продолжит пример кода, показанного в [веб-API, который вызывает веб-API: получить маркер для приложения](scenario-web-api-call-api-acquire-token.md). Код вызывается в действиях контроллеров API. Он вызывает нисходящий API с именем *ToDoList*.

 После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Следующий код продолжит пример кода, показанного в [веб-API, который вызывает веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md). Код вызывается в действиях контроллеров API. Он вызывает нисходящий API MS Graph.

После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Пример, демонстрирующий этот поток с помощью MSAL Python, пока недоступен.

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-API, который вызывает веб-API: переместить в рабочую среду](scenario-web-api-call-api-production.md)

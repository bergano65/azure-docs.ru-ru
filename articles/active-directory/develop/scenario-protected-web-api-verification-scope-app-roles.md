---
title: Проверка областей и защищенных ролей приложений веб-API | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-API и настроить код приложения.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768119"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Защищенный веб-API: Проверка областей и ролей приложений

В этой статье описывается, как можно добавить авторизацию в веб-API. Эта защита гарантирует, что API будет вызываться только:

- Приложения от имени пользователей, имеющих правильные области.
- Управляющие приложения, имеющие правильные роли приложений.

> [!NOTE]
> Фрагменты кода из этой статьи извлекаются из следующих примеров, которые полностью функционируют:
>
> - [ASP.NET Core добавочный учебник по веб-API на сайте](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) GitHub
> - [Пример веб-API ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Чтобы защитить веб-API ASP.NET или ASP.NET Core, необходимо добавить атрибут `[Authorize]` в один из следующих элементов:

- Сам контроллер, если требуется защитить все действия контроллера
- Действие отдельного контроллера для API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но эта защита недостаточно. Он гарантирует только то, что ASP.NET и ASP.NET Core проверить маркер. Ваш API должен проверить, что маркер, используемый для вызова API, запрашивается с ожидаемыми утверждениями. Эти утверждения в частности требуют проверки:

- *Области* , если API вызывается от имени пользователя.
- *Роли приложения* , если API можно вызывать из управляющего приложения.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в интерфейсах API, вызываемых от имени пользователей

Если клиентское приложение вызывает API от имени пользователя, API-интерфейс должен запросить токен носителя с конкретными областями для API. Дополнительные сведения см. в разделе [Конфигурация кода | Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

Метод `VerifyUserHasAnyAcceptedScope` выполняет действия, аналогичные приведенным ниже.

- Убедитесь, что имеется утверждение с именем `http://schemas.microsoft.com/identity/claims/scope` или `scp`.
- Убедитесь, что утверждение имеет значение, которое содержит область, ожидаемую API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Предыдущий [пример кода](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) предназначен для ASP.NET Core. Для ASP.NET просто замените `HttpContext.User` `ClaimsPrincipal.Current`и замените тип утверждения `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`. См. также фрагмент кода ниже в этой статье.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Проверка ролей приложения в интерфейсах API, которые вызываются приложениями управляющей программы

Если веб-API вызывается с помощью [управляющего приложения](scenario-daemon-overview.md), ему должно быть назначено разрешение приложения для веб-API. Как показано в окне [предоставление разрешений приложению (роли приложений)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), API предоставляет такие разрешения. Одним из примеров является роль приложения `access_as_application`.

Теперь необходимо, чтобы ваш API проверял, что полученный маркер содержит утверждение `roles` и что это утверждение имеет ожидаемое значение. Код проверки аналогичен коду, который проверяет делегированные разрешения, за исключением того, что действие контроллера проверяет роли, а не области.

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Метод `ValidateAppRole` может выглядеть следующим образом:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

На этот раз фрагмент кода предназначен для ASP.NET. Для ASP.NET Core просто замените `ClaimsPrincipal.Current` на `HttpContext.User`и замените имя утверждения `"roles"` на `"http://schemas.microsoft.com/identity/claims/roles"`. См. также фрагмент кода, приведенный ранее в этой статье.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Принимаются маркеры только для приложений, если веб-API должен вызываться только управляющими приложениями

Пользователи также могут использовать утверждения ролей в шаблонах назначения пользователей, как показано в [этой статьи: Добавление ролей приложения в приложение и их получение в токене](howto-add-app-roles-in-azure-ad-apps.md). Если роли могут быть назначены обоим, проверка ролей позволит приложениям входить в систему как пользователи и пользователи для входа в качестве приложений. Для предотвращения этой путаницы рекомендуется объявлять разные роли для пользователей и приложений.

Если вы хотите, чтобы только управляющие приложения вызывали веб-API, добавьте условие, которое маркер является маркером только для приложения при проверке роли приложения.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Проверка обратного условия позволяет только приложениям, которые входят в систему пользователя, вызывать ваш API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-protected-web-api-production.md)

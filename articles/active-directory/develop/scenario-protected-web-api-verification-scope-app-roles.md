---
title: Проверка областей и ролей приложений в защищенном веб-API | Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-API и настроить код своего приложения.
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
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845141"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Защищенный веб-API: проверка областей и ролей приложений

В этой статье описывается, как можно добавить авторизацию в веб-API. Эта защита гарантирует, что API будет вызываться только:

- приложениями от имени пользователей, имеющих нужные области;
- управляющими приложениями, имеющими правильные роли приложений.

> [!NOTE]
> Фрагменты кода из этой статьи извлекаются из следующих полностью функциональных примеров:
>
> - [Пошаговое руководство по веб-API ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) на сайте GitHub
> - [Пример веб-API ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Чтобы защитить веб-API ASP.NET или ASP.NET Core, необходимо добавить атрибут `[Authorize]` в один из следующих элементов:

- сам контроллер, если требуется защитить все действия контроллера;
- действие отдельного контроллера для API.

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но этой защиты недостаточно. Она гарантирует только то, что ASP.NET и ASP.NET Core проверят маркер. API необходимо проверить, что маркер, используемый для вызова API, запрашивается с ожидаемыми утверждениями. В частности, требуют проверки следующие утверждения.

- *Области*, если API вызывается от имени пользователя.
- *Роли приложения*, если API можно вызывать из управляющего приложения.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в интерфейсах API, вызываемых от имени пользователей

Если клиентское приложение вызывает API от имени пользователя, API должен запросить токен носителя с конкретными областями для API. Дополнительные сведения см. в [Конфигурация кода | Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token).

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

Метод `VerifyUserHasAnyAcceptedScope` делает нечто вроде следующего.

- Проверьте, существует ли утверждение с именем `http://schemas.microsoft.com/identity/claims/scope` или `scp`.
- Проверьте, что утверждение имеет значение, которое содержит область, ожидаемую API.

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

Предыдущий [пример кода](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) предназначен для ASP.NET Core. Для ASP.NET просто замените `HttpContext.User` на `ClaimsPrincipal.Current` и замените тип утверждения `"http://schemas.microsoft.com/identity/claims/scope"` на `"scp"`. См. также фрагмент кода ниже в этой статье.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Проверка ролей приложения в интерфейсах API, которые вызываются приложениями управляющей программы

Если веб-API вызывается [приложением управляющей программы](scenario-daemon-overview.md), это приложение должно требовать разрешения приложения для веб-API. Как показано в разделе [Предоставление разрешений приложения (роли приложений)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), API предоставляет такие разрешения. Одним из примеров является роль приложения `access_as_application`.

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

На этот раз фрагмент кода предназначен для ASP.NET. Для ASP.NET просто замените `ClaimsPrincipal.Current` на `HttpContext.User` и замените имя утверждения `"roles"` на `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"`. См. также фрагмент кода выше в этой статье.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Принятие маркеров только для приложений, если веб-API должен вызываться только управляющими приложениями

Пользователи также могут использовать утверждения ролей в шаблонах назначения пользователей, как показано в [Практическом руководстве. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](howto-add-app-roles-in-azure-ad-apps.md). Если роли могут быть назначены обоим, проверка ролей позволит приложениям входить в систему в качестве пользователей, а пользователям входить в качестве приложений. Для предотвращения этой путаницы рекомендуется объявлять разные роли для пользователей и приложений.

Если вы хотите, чтобы только управляющие приложения вызывали веб-API, добавьте условие, согласно которому маркер является маркером только для приложения при проверке роли приложения.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Проверка обратного условия позволяет только приложениям, которые входят в систему в качестве пользователя, вызывать ваш API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос в рабочую среду](scenario-protected-web-api-production.md)

---
title: Защищенный веб-API — конфигурация кода приложения | Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488835"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Защищенный веб-API: Добавление авторизации в API

В этой статье описывается, как можно добавить авторизацию в веб-API. Эта защита гарантирует, что API будет вызываться только:

- Приложения от имени пользователей, имеющих правильные области.
- Управляющие приложения, имеющие правильные роли приложений.

> [!NOTE]
> Фрагменты кода из этой статьи извлекаются из следующих примеров, которые полностью функционируют
>
> - [ASP.NET Core добавочный учебник по веб-API на сайте](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) GitHub
> - [Пример веб-API ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Чтобы защитить веб-API ASP.NET/ASP.NET Core, необходимо добавить `[Authorize]` атрибут в один из следующих элементов:

- Сам контроллер, если требуется защитить все действия контроллера
- Действие отдельного контроллера для API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но эта защита недостаточно. Гарантируется, что маркер будет проверяться только тем, что ASP.NET/ASP.NET Core. Ваш API должен проверить, что маркер, используемый для вызова веб-API, запрашивал ожидаемые утверждения, в частности:

- *Области*, если API вызывается от имени пользователя.
- *Роли приложения*, если API можно вызывать из управляющего приложения.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в интерфейсах API, вызываемых от имени пользователей

Если API вызывается клиентским приложением от имени пользователя, ему необходимо запросить токен носителя с конкретными областями для API. (См. раздел [Конфигурация кода | Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

`VerifyUserHasAnyAcceptedScope` Метод будет выполнять нечто вроде следующего:

- Убедитесь в наличии утверждения с именем `http://schemas.microsoft.com/identity/claims/scope` или. `scp`
- Убедитесь, что утверждение имеет значение, которое содержит область, ожидаемую API.

```CSharp
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

Этот [пример кода](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) предназначен для ASP.NET Core. Для ASP.NET просто `HttpContext.User` замените на `ClaimsPrincipal.Current`и `"http://schemas.microsoft.com/identity/claims/scope"` замените тип утверждения на. `"scp"` (См. также фрагмент кода далее в этой статье.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Проверка ролей приложений в интерфейсах API, вызванных приложениями управляющей программы

Если веб-API вызывается с помощью [управляющего приложения](scenario-daemon-overview.md), ему должно быть назначено разрешение приложения для веб-API. Мы рассмотрели [разрешения приложений (роли приложений)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , которые API предоставляет таким разрешениям (например, `access_as_application` роль приложения).
Теперь вы должны убедиться, что полученный маркер содержит `roles` утверждение и что это утверждение имеет ожидаемое значение. Код, выполняющий эту проверку, аналогичен коду, который проверяет делегированные разрешения, за исключением того, что, `scopes`вместо проверки, действие контроллера будет `roles`проверять следующее:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` Метод может выглядеть следующим образом:

```CSharp
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

На этот раз фрагмент кода предназначен для ASP.NET. Для ASP.NET Core просто `ClaimsPrincipal.Current` замените на `HttpContext.User`и `"roles"` замените имя утверждения на. `"http://schemas.microsoft.com/identity/claims/roles"` (См. также фрагмент кода, приведенный выше в этой статье.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Принимаются маркеры только для приложений, если веб-API должен вызываться только управляющими приложениями

`roles` Утверждение также используется для пользователей в шаблонах назначения пользователей. (См [. раздел как Добавьте роли приложения в приложение и получите их в маркере](howto-add-app-roles-in-azure-ad-apps.md).) Поэтому только проверка ролей позволит приложениям входить в систему как пользователей и наоборот, если роли могут быть назначены обоим пользователям. Для предотвращения этой путаницы рекомендуется объявлять разные роли для пользователей и приложений.

Если вы хотите разрешить только приложениям управляющей программы вызывать веб-API, добавьте условие при проверке роли приложения, чтобы маркер был маркером только для приложения:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Проверка обратного условия позволит только приложениям, которые входят в систему пользователя, вызывать ваш API.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-protected-web-api-production.md)

---
title: Защищенный веб-API - код конфигурации приложений | Azure Active Directory
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
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551536"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Защищенный веб-API: Добавление авторизации к вашему API

В этой статье описывается, как можно добавить авторизации в веб-API. Такая защита гарантирует, что этот API называется только:

- Приложений от имени пользователей с соответствующими областями.
- Управляющая программа приложения, у роли приложения.

Чтобы защитить веб-API ASP.NET/ASP.NET Core, необходимо добавить `[Authorize]` атрибута один из следующих:

- Сам контроллер, если требуется, чтобы все действия контроллера для защиты
- Действие отдельного контроллера для API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но такая защита недостаточно. Это только гарантирует, что ASP.NET/ASP.NET Core проверит маркер. API должен проверить, что токен, используемый для вызова веб-запросе API с утверждениями, ожидается, что, в частности:

- *Областей*, если вызов API от имени пользователя.
- *Роли приложения*, если API могут вызываться из управляющую программу.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в API, вызываемых от лица пользователей

Если API вызывается с клиентского приложения от имени пользователя, ему необходимо запросить маркер носителя, который имеет определенные области для API. (См. в разделе [код конфигурации | Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token).)

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

`VerifyUserHasAnyAcceptedScope` Метод сделать примерно следующим образом:

- Убедитесь, что утверждение с именем `http://schemas.microsoft.com/identity/claims/scope` или `scp`.
- Убедитесь, что утверждения имеет значение, содержащее область, требуется API.

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

Этот пример кода предназначен для ASP.NET Core. Для ASP.NET, просто замените `HttpContext.User` с `ClaimsPrincipal.Current`и замените тип утверждения `"http://schemas.microsoft.com/identity/claims/scope"` с `"scp"`. (См. также в фрагменте кода далее в этой статье.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Проверка роли приложения в API, вызываемые приложений управляющей программы

Если веб-API вызывается [управляющую программу](scenario-daemon-overview.md), этого приложения требуется разрешение приложения в веб-API. Мы наблюдали за [предоставление разрешения для приложений (роли приложений)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) что API предоставляет такие разрешения (например, `access_as_application` роли приложения).
Теперь необходимо убедиться, что он получен маркер содержит API-интерфейсов `roles` утверждения и что это утверждение имеет значение, ожидаемое. Код, выполнив эту проверку похож на код, который проверяет делегированные разрешения, за одним исключением, вместо проверки для `scopes`, будет проверяться действия вашего контроллера `roles`:

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

`ValidateAppRole()` Метод может быть что-то подобное:

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

Этот пример кода предназначен для ASP.NET. ASP.NET Core, просто замените `ClaimsPrincipal.Current` с `HttpContext.User`и замените `"roles"` имя с помощью утверждения `"http://schemas.microsoft.com/identity/claims/roles"`. (См. также в фрагменте кода выше в этой статье.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Принимая маркеры только для приложений в том случае, если веб-API должен вызываться только приложениями управляющей программы

`roles` Утверждение также используется для пользователей в шаблонах назначения пользователя. (См. в разделе [как: Добавление ролей приложения в приложении и получать их в маркере](howto-add-app-roles-in-azure-ad-apps.md).) Поэтому просто проверка ролей позволит приложений войти в качестве пользователей и наоборот, если может быть назначен как роли. Рекомендуется объявить разные роли для пользователей и приложений избежать этой путаницы.

Если вы хотите разрешить только тем приложениям управляющей программы для вызова веб-API, добавьте условие, при проверке роли приложения, что маркер является маркером только для приложений:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Проверка условия обратный позволит только приложения, которые выполняют вход пользователя для вызова API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-protected-web-api-production.md)

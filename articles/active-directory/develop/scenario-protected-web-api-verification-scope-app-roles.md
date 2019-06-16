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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116458"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>Защищенный веб-API — Добавление авторизации к вашему API

В этой статье описывается, как можно добавить авторизации в веб-API. Такая защита гарантирует, что он вызывается только:

- приложения от лица пользователей с соответствующими областями 
- или приложений управляющей программы с помощью ролей приложения.

Для ASP.NET и ASP.NET Core веб-API для защиты, вам потребуется добавить `[Authorize]` атрибут:

- Если требуется, чтобы все действия контроллера для защиты контроллера
- или действия отдельного контроллера для API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но такая защита недостаточно. Он только guaranties, ASP.NET и ASP.NET Core проверит маркер. API должен проверить, что токен, используемый для вызова веб-API был запрошен с утверждениями, ожидается, что, в частности:

- **областей** при вызове API от имени пользователя
- **роли приложения** Если API могут вызываться из управляющую программу.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в API, вызываемых от лица пользователей

Если API вызывается клиентское приложение от имени пользователя, то ему необходимо запросить токен носителя с определенной области для API (см. в разделе [код конфигурации | Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` Метод сделать примерно следующим образом:

- Убедитесь в наличии утверждений с именем `http://schemas.microsoft.com/identity/claims/scope` или `scp`
- Убедитесь, что утверждения имеет значение, содержащее область, требуется API.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

Этот пример кода предназначен для ASP.NET Core. Для ASP.NET просто заменить `HttpContext.User` по `ClaimsPrincipal.Current`и тип утверждения `"http://schemas.microsoft.com/identity/claims/scope"` по `"scp"` (см. также: приведенный ниже фрагмент кода)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Проверка роли приложения в API, вызываемые приложений управляющей программы

Если веб-API вызывается [управляющее приложение](scenario-daemon-overview.md), а затем этого приложения требуются разрешения в приложении для веб-API. Мы наблюдали за [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-] что API предоставляет такие разрешения (например, как `access_as_application` роли приложения).
Теперь необходимо убедиться, что он получен маркер содержит API-интерфейсов `roles` утверждений и что это утверждение имеет значение, ожидаемое. Код, выполнив эту проверку похож на код, который проверяет делегированные разрешения, за одним исключением, вместо проверки для `scopes`, будет проверяться действия вашего контроллера `roles`:

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
    // In this case we look for a `role` value of `access_as_application`
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

Этот пример кода предназначен для ASP.NET. ASP.NET Core, просто замените `ClaimsPrincipal.Current` по `HttpContext.User` и `"roles"` утверждения имени по `"http://schemas.microsoft.com/identity/claims/roles"` (см. также: приведенном выше фрагменте кода)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Принятие только маркеры приложений, если веб-API должен вызываться только приложениями управляющей программы

`roles` Утверждение также используется для пользователей в шаблонах назначения пользователя (см. в разделе [как: Добавление ролей приложения в приложении и получать их в маркере](howto-add-app-roles-in-azure-ad-apps.md)). Поэтому просто проверка ролей позволит приложений войти в качестве пользователей и наоборот, если может быть назначен как роли. Мы рекомендуем использовать разные роли, объявленным для пользователей и приложений избежать этой путаницы.

Если вы хотите разрешить приложениям управляющей программы для вызова веб-API, необходимо добавить условие, при проверке роли приложения, что маркер является маркером только для приложений:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Проверка условия обратный позволит только приложения, которые выполняют вход пользователя, для вызова API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-protected-web-api-production.md)

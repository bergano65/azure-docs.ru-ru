---
title: Проверка областей и защищенных ролей приложений Web API Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-aPI и настроить код приложения.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768119"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Защищенный веб-aPI: Проверка областей и ролей приложений

В этой статье описывается, как можно добавить авторизацию в web-aPI. Эта защита гарантирует, что API вызывается только:

- Приложения от имени пользователей, имеющих нужные области.
- Приложения Daemon, которые имеют правильные роли приложения.

> [!NOTE]
> Фрагменты кода из этой статьи извлекаются из следующих образцов, которые полностью функциональны:
>
> - [ASP.NET core Web API дополнительный учебник](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) по GitHub
> - [ASP.NET образец Web API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Чтобы защитить ASP.NET или ASP.NET web-API `[Authorize]` Core, необходимо добавить атрибут к одному из следующих элементов:

- Сам контроллер, если вы хотите, чтобы все действия контроллера были защищены
- Индивидуальное действие контроллера для Вашего API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Но этой защиты недостаточно. Он гарантирует только то, что ASP.NET и ASP.NET Core проверяют токен. API должен убедиться, что токен, используемый для вызова API, запрашивается с ожидаемыми претензиями. Эти претензии, в частности, нуждаются в проверке:

- *Области,* если API называется от имени пользователя.
- Роли *приложения,* если API можно вызвать из приложения daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Проверка областей в AIS, вызванных от имени пользователей

Если клиентское приложение вызывает Ваш API от имени пользователя, API необходимо запросить маркер носителя, который имеет определенные области для API. Для получения дополнительной информации [см. Токен носителя](scenario-protected-web-api-app-configuration.md#bearer-token).

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

Метод `VerifyUserHasAnyAcceptedScope` делает что-то вроде следующих шагов:

- Проверить есть претензии `http://schemas.microsoft.com/identity/claims/scope` имени `scp`или .
- Проверка претензии имеет значение, содержащее область, ожидаемую API.

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

Предыдущий [код образца](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) предназначен для ASP.NET Core. Для ASP.NET, `HttpContext.User` просто `ClaimsPrincipal.Current`заменить, и `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`заменить тип претензии с . Также смотрите фрагмент кода позже в этой статье.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Проверка ролей приложений в AI, называемых приложениями daemon

Если ваш веб-API вызывается [приложением daemon,](scenario-daemon-overview.md)это приложение должно требовать разрешения приложения для вашего веб-API. Как показано в [разоблачающих разрешениях приложений (роли приложений),](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)API предоставляет такие разрешения. Одним из `access_as_application` примеров является роль приложения.

Теперь необходимо, чтобы API проверил, что токен, который он получает, содержит `roles` претензию и что эта претензия имеет ожидаемое значение. Код проверки аналогичен коду, который проверяет делегированные разрешения, за исключением того, что тесты действий контроллера для ролей, а не областей:

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

Метод `ValidateAppRole` может быть что-то вроде этого:

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

На этот раз фрагмент кода предназначен для ASP.NET. Для ASP.NET Core, `ClaimsPrincipal.Current` `HttpContext.User`просто заменить `"roles"` , `"http://schemas.microsoft.com/identity/claims/roles"`и заменить имя претензии с . Также смотрите фрагмент кода ранее в этой статье.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Прием токенов только для приложений, если web API должен называться только приложениями daemon

Пользователи также могут использовать требования ролей в шаблонах назначения пользователя, как показано в [Как: Добавить роли приложения в приложении и получать их в маркере.](howto-add-app-roles-in-azure-ad-apps.md) Если роли присваиваются обоим, проверка ролей позволит приложениям войти в систему как пользователям, так и пользователям для вхвнутри в качестве приложений. Мы рекомендуем вам объявить различные роли для пользователей и приложений, чтобы предотвратить эту путаницу.

Если вы хотите, чтобы только приложения daemon звонили на ваш веб-API, добавьте условие, что маркер является маркером только для приложений при проверке роли приложения.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Проверка обратного состояния позволяет звонить вашему API только в приложениях, всопхивающих пользователем.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос в рабочую среду](scenario-protected-web-api-production.md)

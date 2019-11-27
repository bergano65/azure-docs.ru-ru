---
title: Расширенное использование аутентификации и авторизации в Службе приложений Azure | Документация Майкрософт
description: В этой статье показано, как настроить проверку подлинности и авторизацию в службе приложений и получить утверждения пользователей, а также различные токены.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/24/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f453a0276a3448273964a589112e21ca5665c2d2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900139"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Расширенное использование проверки подлинности и авторизации в Службе приложений Azure

В этой статье показано, как настроить встроенные [проверку подлинности и авторизацию в службе приложений](overview-authentication-authorization.md), а также управлять удостоверениями, используя приложение. 

Чтобы быстро приступить к работе, ознакомьтесь с одним из следующих руководств:

* [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Linux)](containers/tutorial-auth-aad.md)
* [Настройка приложения для использования имени входа Azure Active Directory](configure-authentication-provider-aad.md)
* [Настройка приложения для использования имени входа Facebook](configure-authentication-provider-facebook.md)
* [Настройка приложения для использования имени входа Google](configure-authentication-provider-google.md)
* [Настройка приложения для использования входа по учетной записи Майкрософт](configure-authentication-provider-microsoft.md)
* [Настройка приложения для использования имени входа Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Использование нескольких поставщиков входа

На портале нельзя напрямую настроить несколько поставщиков входа для пользователей (например, через Facebook и Twitter). Однако эту функцию можно легко добавить к функциональным возможностям вашего приложения. Для этого необходимо сделать следующее:

Во-первых, на странице **Authentication / Authorization** (Проверка подлинности и авторизация) на портале Azure настройте все поставщики удостоверений, которые нужно включить.

В раскрывающемся списке **Action to take when request is not authenticated** (Предпринимаемое действие, если проверка подлинности для запроса не выполнена) выберите **Разрешить анонимные запросы (нет действия)** .

На странице входа, на панели навигации или в любом другом расположении приложения добавьте ссылку входа для каждого включенного поставщика (`/.auth/login/<provider>`). Пример:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Когда пользователь щелкнет одну из этих ссылок, откроется соответствующая страница для входа.

Чтобы перенаправить пользователя после входа по пользовательскому URL-адресу, используйте параметр строки запроса `post_login_redirect_url` (не следует путать с URI перенаправления в конфигурации поставщика удостоверений). Например, чтобы перенаправить пользователя к `/Home/Index` после входа в систему, используйте следующий код HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Проверка токенов от поставщиков

При входе с помощью клиента приложение входит в систему поставщика вручную, а затем отправляет токен проверки подлинности службе приложений для проверки (см. [Поток проверки подлинности](overview-authentication-authorization.md#authentication-flow)). Эта проверка сама по себе не предоставляет вам доступ к требуемым ресурсам приложения, но успешная проверка даст вам токен сеанса, который вы можете использовать для доступа к ресурсам приложений. 

Чтобы проверить токен поставщика, для приложения службы приложений сначала нужно настроить требуемый поставщик. Получив токен проверки подлинности у своего поставщика, во время выполнения отправьте токен по адресу `/.auth/login/<provider>` для проверки. Пример: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Формат токена незначительно отличается в соответствии с поставщиком. Дополнительные сведения см. в таблице, приведенной ниже.

| Значение поставщика | Требуется в тексте запроса | Комментарии |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Свойство `expires_in` необязательное. <br/>При запросе маркера из служб Live всегда запрашиваются области `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | Свойство `authorization_code` необязательное. Если указано, при необходимости оно может сопровождаться свойством `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Используйте допустимый [токен доступа пользователя](https://developers.facebook.com/docs/facebook-login/access-tokens) из Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

При успешной проверке токена поставщика API возвращается с `authenticationToken` в тексте ответа, который является вашим токеном сеанса. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Получив этот токен сеанса, вы можете получить доступ к защищенным ресурсам приложений, добавив заголовок `X-ZUMO-AUTH` к HTTP-запросам. Пример: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Выход из сеанса

Пользователи могут сделать выход, отправив запрос `GET` в конечную точку `/.auth/logout` приложения. Запрос `GET` выполняет следующие действия:

- Очищает файлы cookie проверки подлинности в текущем сеансе.
- Удаляет текущие маркеры пользователя из хранилища маркеров.
- Выполняет выход в поставщике удостоверений на стороне сервера для Azure Active Directory и Google.

Здесь представлена ссылка для простого выхода на веб-странице:

```HTML
<a href="/.auth/logout">Sign out</a>
```

После успешного выхода клиент по умолчанию перенаправляется на URL-адрес `/.auth/logout/done`. Можно изменить страницу перенаправления после выхода, добавив параметр запроса `post_logout_redirect_uri`. Пример:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Рекомендуется [закодировать](https://wikipedia.org/wiki/Percent-encoding) значение `post_logout_redirect_uri`.

При использовании полного URL-адреса он должен размещаться в одном и том же домене или быть настроенным в качестве разрешенного URL-адреса внешнего перенаправления для приложения. В следующем примере показано перенаправление на адрес `https://myexternalurl.com`, который не размещен в одном и том же домене:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Выполните следующую команду в [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Сохранение фрагментов URL-адреса

После входа в приложение пользователи обычно желают быть перенаправленными в один и тот же раздел той же страницы, например `/wiki/Main_Page#SectionZ`. Но так как [фрагменты URL-адреса](https://wikipedia.org/wiki/Fragment_identifier) (например, `#SectionZ`) никогда не отправляются на сервер, они не сохраняются по умолчанию после завершения входа OAuth и перенаправления обратно в приложение. Это неудобно для пользователей, когда им снова нужно перейти в требуемую закладку. Это ограничение распространяется на все решения аутентификации на стороне сервера.

При использовании аутентификации службы приложений можно сохранять фрагменты URL-адресов во время входа OAuth. Чтобы сделать это, установите для параметра приложения `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` значение `true`. Это можно сделать на [портале Azure](https://portal.azure.com) или просто выполнив следующую команду в [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Доступ к утверждениям пользователей

Служба приложений передает утверждения пользователей в приложение с помощью специальных заголовков. Эти заголовки нельзя настроить с помощью внешних запросов. Они присутствуют, только если это установлено службой приложений. Некоторые примеры заголовков включают:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Сведения из этих заголовков можно получить с помощью кода, написанного на любом языке или в любой платформе. Для приложений ASP.NET 4.6 автоматически настраивается класс **ClaimsPrincipal** с соответствующими значениями. Однако ASP.NET Core не предоставляет по промежуточного слоя для проверки подлинности, которое интегрируется с утверждениями пользователя службы приложений. Обходной путь см. в разделе [максимерауиллер. Azure. AppService. еасяус](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

В приложении можно также получить дополнительные сведения о пользователе, прошедшем проверку подлинности, путем вызова `/.auth/me`. Пакеты SDK для серверной части мобильных приложений предоставляют вспомогательные методы для работы с этими данными  Дополнительные сведения см. Дополнительные сведения см. в разделах [Практическое руководство. Использование утверждений аутентификации для таблиц](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) и [Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Извлечение токенов в коде приложения

Токены, предоставляющиеся поставщиком, вводятся в заголовке запроса из кода сервера, что позволяет легко получить к ним доступ. В следующей таблице показаны возможные заголовки токена.

| Поставщик | Имена заголовков |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Токен Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Учетная запись Майкрософт | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

В коде клиента (например, мобильного приложения или JavaScript в браузере) отправьте HTTP-запрос `GET` к `/.auth/me`. В возвращаемом JSON-файле будут содержаться предоставляемые поставщиком токены.

> [!NOTE]
> Токены доступа предназначены для получения доступа к ресурсам поставщика, поэтому они заданы, только если настройка поставщика осуществляется с помощью секрета клиента. Дополнительные сведения о том, как получить токены обновления, см. в разделе об обновлении маркеров доступа.

## <a name="refresh-identity-provider-tokens"></a>Обновление маркеров поставщика удостоверений

По истечении срока действия маркера доступа вашего провайдера (а не [токена сеанса](#extend-session-token-expiration-grace-period)) необходимо повторно подтвердить подлинность пользователя, прежде чем снова использовать этот маркер. Истечения срока действия токена можно избежать, выполнив вызов `GET` к конечной точке приложения `/.auth/refresh`. В этом случае служба приложений автоматически обновляет токены доступа в хранилище токенов для пользователя, прошедшего проверку подлинности. В результате последующих запросов на токены с помощью кода приложения будут возвращаться обновленные токены. Однако для правильного обновления токена в хранилище токенов должны содержаться [токены обновления](https://auth0.com/learn/refresh-tokens/) для поставщика. Способ получения токенов обновления документируется каждым поставщиком, но ниже приведено краткое описание.

- **Google**. Добавьте параметр строки запроса `access_type=offline` к вызову API `/.auth/login/google`. Если используется пакет SDK для мобильных служб, можно добавить параметр к одной из перегрузок `LogicAsync` (см. в разделе о [токенах обновления Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**. Не предоставляет токены обновления. Срок действия токенов с долгим временем существования истекает через 60 дней (см. раздел об [истечении и продлении срока действия токенов доступа Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**. Срок действия токенов доступа не истекает (см. раздел о [часто задаваемых вопросах о Twitter OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Учетная запись Майкрософт**. [Настраивая параметры проверки подлинности учетной записи Майкрософт](configure-authentication-provider-microsoft.md), выберите область `wl.offline_access`.
- **Azure Active Directory**. В [https://resources.azure.com](https://resources.azure.com) сделайте следующее:
    1. В верхней части страницы выберите **Read/Write** (Чтение и запись).
    2. В левой части браузера перейдите к **subscriptions** >  **_\<имя\_подписки_**  > **resourceGroups** >  **_\<имя\_группы\_ресурсов>_**  > **providers** > **Microsoft.Web** > **sites** >  **_\<имя\_приложения>_**  > **config** > **authsettings**. 
    3. Нажмите кнопку **Изменить**.
    4. Измените следующее свойство. Замените _\<app\_id>_ идентификатором приложения Azure Active Directory службы, к которой требуется получить доступ.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Щелкните **Put**. 

После настройки поставщика можно [найти токен обновления и срок действия для токена доступа](#retrieve-tokens-in-app-code) в хранилище токенов. 

Чтобы обновить маркер доступа в любое время, просто вызовите `/.auth/refresh` на любом языке. В следующем фрагменте кода jQuery используется для обновления токенов доступа из клиента JavaScript.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Если пользователь отменяет разрешения, предоставленные приложению, вызов `/.auth/me` может завершиться ошибкой `403 Forbidden`. Чтобы выполнить диагностику ошибок, проверьте дополнительные сведения в журналах приложений.

## <a name="extend-session-token-expiration-grace-period"></a>Продление льготного периода срока действия токена сеанса

Сеанс проверки подлинности истекает через 8 часов. По истечении срока действия авторизованного сеанса по умолчанию предоставляется 72-часовой льготный период. В течение этого льготного периода токен сеанса можно обновить с помощью Службы приложений без повторной проверки подлинности пользователя. Если токен сеанса становится недействительным, вы можете просто вызвать `/.auth/refresh`, и вам не нужно самостоятельно отслеживать истечение срока действия токена. После завершения 72-часового льготного периода пользователь должен выполнить вход в систему, чтобы получить действительный токен сеанса.

Если 72 часов не достаточно, можно продлить этот льготный период. Значительное продление этого периода может повлиять на уровень безопасности (например, токен проверки подлинности может быть утерян или украден). В связи с этим следует оставить значение по умолчанию (72 часа) или установить для периода продления наименьшее значение.

Чтобы продлить льготный период истечения срока действия по умолчанию, выполните следующую команду в [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Льготный период применяется только к сеансу, проверка подлинности для которого осуществлялась с помощью службы приложений, а не токенов, предоставляемых поставщиками удостоверений. Льготный период на предоставленные поставщиком токены, срок действия которых истек, не распространяется. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ограничение домена учетных записей для входа

Учетные записи Майкрософт и Azure Active Directory позволяют выполнять вход из нескольких доменов. Например, учетная запись Майкрософт позволяет выполнять вход с помощью учетных записей _outlook.com_, _live.com_ и _hotmail.com_. Azure AD позволяет любому числу пользовательских доменов для учетных записей входа. Тем не менее, вам может потребоваться ускорить работу пользователей с вашей собственной фирменной страницей входа в Azure AD (например, `contoso.com`). Чтобы предложить доменное имя учетных записей входа, выполните следующие действия.

В разделе [https://resources.azure.com](https://resources.azure.com) перейдите к **subscriptions** >  **_\<имя\_подписки_**  > **resourceGroups** >  **_\<имя\_группы\_ресурсов>_**  > **providers** > **Microsoft.Web** > **sites** >  **_\<имя\_приложения>_**  > **config** > **authsettings**. 

Щелкните **Edit** (Изменить), измените следующее свойство и выберите **Put**. Обязательно замените _\<domain\_имя>_ необходимым доменом.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Этот параметр добавляет `domain_hint` параметр строки запроса к URL-адресу перенаправления имени входа. 

> [!IMPORTANT]
> Клиент может удалить параметр `domain_hint` после получения URL-адреса перенаправления, а затем войти в другой домен. Поэтому хотя эта функция удобна, она не является функцией безопасности.
>

## <a name="authorize-or-deny-users"></a>Авторизовать или запретить пользователей

Хотя служба приложений отвечает за простейший вариант авторизации (т. е. отклонять запросы без проверки подлинности), приложение может потребовать более детального поведения авторизации, например ограничение доступа только к определенной группе пользователей. В некоторых случаях необходимо написать пользовательский код приложения, чтобы разрешить или запретить доступ для пользователя, выполнившего вход. В других случаях служба приложений или поставщик удостоверений могут помочь без внесения изменений в код.

- [Уровень сервера](#server-level-windows-apps-only)
- [Уровень поставщика удостоверений](#identity-provider-level)
- [Уровень приложения](#application-level)

### <a name="server-level-windows-apps-only"></a>Уровень сервера (только для приложений Windows)

Для любого приложения Windows можно определить поведение авторизации веб-сервера IIS, изменив файл *Web. config* . Приложения Linux не используют IIS и не могут быть настроены с помощью *Web. config*.

1. Перейдите на страницу `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. В обозревателе браузера файлов службы приложений перейдите на *сайт Site/wwwroot*. Если файл *Web. config* не существует, создайте его, выбрав **+**  > **New File**. 

1. Выберите карандаш для *файла Web. config* , чтобы изменить его. Добавьте следующий код конфигурации и нажмите кнопку **сохранить**. Если *файл Web. config* уже существует, просто добавьте элемент `<authorization>` со всеми элементами. Добавьте учетные записи, которые вы хотите разрешить, в элемент `<allow>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Уровень поставщика удостоверений

Поставщик удостоверений может предоставить некоторую проверку подлинности с помощью ключа. Пример:

- Для [службы приложений Azure](configure-authentication-provider-aad.md)вы можете [управлять доступом на уровне предприятия](../active-directory/manage-apps/what-is-access-management.md) непосредственно в Azure AD. Инструкции см. [в разделе Удаление доступа пользователя к приложению](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Для [Google](configure-authentication-provider-google.md), проектов Google API, входящих в [организацию](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) , можно настроить разрешение доступа только для пользователей в вашей организации (см. [страницу поддержки **OAuth 2,0** в Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>На уровне приложения

Если любой из других уровней не обеспечивает необходимую авторизацию или если ваша платформа или поставщик удостоверений не поддерживается, необходимо написать пользовательский код для авторизации пользователей на основе [утверждений пользователей](#access-user-claims).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Windows)](app-service-web-tutorial-auth-aad.md)
> [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Linux)](containers/tutorial-auth-aad.md)

---
title: Azure AD B2C (MSAL Android) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о конкретных соображениях при использовании Azure AD B2C в библиотеке подлинности Microsoft для Android (MSAL). Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696527"
---
# <a name="use-msal-for-android-with-b2c"></a>Используйте MSAL для Android с B2C

Библиотека аутентификации Майкрософт (MSAL) позволяет разработчикам приложений аутентифицировать пользователей с помощью удостоверений социальных сетей и локальных удостоверений, используя [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C — это служба управления удостоверениями. Используйте его для настройки и контроля, как клиенты регистрироваться, войти в систему и управлять своими профилями, когда они используют ваши приложения.

## <a name="configure-known-authorities-and-redirect-uri"></a>Настройка известных органов власти и перенаправление URI

В MSAL для Android политики B2C (путешествия пользователей) настроены как отдельные органы власти.

С учетом приложения B2C, которое имеет две политики:
- Регистрация / Регистрация в
    * Называется`B2C_1_SISOPolicy`
- Изменить профиль
    * Называется`B2C_1_EditProfile`

Файл конфигурации для приложения `authorities`объявляет два . По одному для каждой политики. Собственность `type` каждого органа `B2C`власти .

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

Необходимо `redirect_uri` зарегистрироваться в конфигурации приложения, `AndroidManifest.xml` а также для поддержки перенаправления во время [потока гранта кода авторизации.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)

## <a name="initialize-ipublicclientapplication"></a>Инициализация приложения Для государственных клиентов

`IPublicClientApplication`построен по заводскому методу, чтобы конфигурация приложения была разогнана асинхронно.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Интерактивно приобрести токен

Чтобы приобрести токен в интерактивном `AcquireTokenParameters` режиме с MSAL, создайте экземпляр и предоставите его методу. `acquireToken` Запрос токенов ниже `default` использует полномочия.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Тихо обновить токен

Чтобы получить токен с помощью MSAL, создайте `AcquireTokenSilentParameters` `acquireTokenSilentAsync` экземпляр и поставите его методу. В `acquireToken` отличие от `authority` метода, необходимо указать, чтобы приобрести токен молча.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Указать политику

Поскольку политики в B2C представлены как отдельные органы, использование политики, `fromAuthority` кроме дефолта, достигается путем указания положения при построении `acquireToken` или `acquireTokenSilent` параметров.  Пример:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Обработка политики изменения паролей

Локальная регистрация учетной записи или поток пользователей для ввоза показывает **'Забытый пароль?'** . При переходе по этой ссылке поток пользователя сброса паролей не активируется автоматически.

Вместо этого в приложение возвращается код ошибки `AADB2C90118`. Приложение должно обрабатывать этот код ошибки, запустив определенный поток пользователей, который сбрасывает пароль.

Чтобы поймать код ошибки сброса пароля, следующая реализация может быть использована внутри вашего: `AuthenticationCallback`

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Использование IAuthenticationResult

Успешное приобретение токенов `IAuthenticationResult` приводит к объекту. Он содержит токен доступа, требования пользователей и метаданные.

### <a name="get-the-access-token-and-related-properties"></a>Получите токен доступа и связанные с ними свойства

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Получить авторизованную учетную запись

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Претензии IdToken

Претензии, возвращенные в IdToken, заполняются службой безопасности (STS), а не MSAL. В зависимости от используемого поставщика идентификационных данных (IdP) некоторые претензии могут отсутствовать. Некоторые ИДП в настоящее `preferred_username` время не предоставляют претензию. Потому что это утверждение используется MSAL для кэширования, значение заполнителя, `MISSING FROM THE TOKEN RESPONSE`используется на своем месте. Для получения дополнительной информации о [претензиях](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)B2C IdToken см.

## <a name="managing-accounts-and-policies"></a>Управление учетными записями и политиками

B2C рассматривает каждую политику как отдельный орган. Таким образом, токены доступа, маркеры обновления и идентификаторы, возвращенные из каждой политики, не являются взаимозаменяемыми. Это означает, что `IAccount` каждая политика возвращает отдельный объект, маркеры которого не могут быть использованы для использования других политик.

Каждая политика `IAccount` добавляет кэш для каждого пользователя. Если пользователь впишется в приложение и вызывает две политики, `IAccount`у него будет два s. Чтобы удалить этого пользователя из кэша, необходимо вызвать `removeAccount()` каждую политику.

При обновлении токенов `acquireTokenSilent`для политики, предоставьте то же `IAccount` самое, `AcquireTokenSilentParameters`что было возвращено из предыдущих вызовов политики в . Предоставление учетной записи, возвращенной другой политикой, приведет к ошибке.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше об B2C Active Directory Azure (Azure AD B2C) в [исканне Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

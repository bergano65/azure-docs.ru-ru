---
title: Защита службы RESTful в Azure AD B2C
titleSuffix: Azure AD B2C
description: Защитите пользовательские REST API заявок на обмен утверждениями в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680381"
---
# <a name="secure-your-restful-services"></a>Защита служб RESTFUL 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

При интеграции REST API в Azure AD B2C пути взаимодействия пользователя необходимо защитить конечную точку REST API с помощью проверки подлинности. Это гарантирует, что только службы, имеющие правильные учетные данные, такие как Azure AD B2C, могут выполнять вызовы к конечной точке REST API.

Узнайте, как интегрировать REST API в пути взаимодействия пользователя Azure AD B2C в ходе [проверки входных данных](custom-policy-rest-api-claims-validation.md) и [добавления REST API обмена утверждениями в статьи с пользовательскими политиками](custom-policy-rest-api-claims-exchange.md) .

В этой статье рассматривается защита REST API с помощью проверки подлинности HTTP Basic, Client Certificate или OAuth2. 

## <a name="prerequisites"></a>Предварительные условия

Выполните действия, описанные в одном из следующих руководств.

- [Интеграция REST APIных запросов обмена утверждениями в Azure AD B2C пути взаимодействия пользователя для проверки вводимых пользователем данных](custom-policy-rest-api-claims-validation.md).
- [Добавление REST API обмена утверждениями в пользовательские политики](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Обычная проверка подлинности HTTP

Обычная проверка подлинности HTTP определяется в [RFC 2617](https://tools.ietf.org/html/rfc2617). Обычная проверка подлинности работает следующим образом: Azure AD B2C отправляет HTTP-запрос с учетными данными клиента в заголовке авторизации. Учетные данные форматируются в виде строки в кодировке Base64 с именем "имя: пароль".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Добавление REST API ключи политики для имени пользователя и пароля

Чтобы настроить REST API технический профиль с обычной проверкой подлинности HTTP, создайте следующие криптографические ключи для хранения имени пользователя и пароля:

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** , а затем выберите Azure AD B2C каталог.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики**и нажмите кнопку **Добавить**.
1. В пункте **Параметры** выберите **Manual** (Вручную).
1. В качестве **имени**введите **рестапиусернаме**.
    Префикс *B2C_1A_* может быть добавлен автоматически.
1. В поле **секрет** введите имя пользователя REST API.
1. Для параметра **Использование ключа** задайте значение **Шифрование**.
1. Щелкните **Создать**.
1. Снова выберите **ключи политики** .
1. Выберите **Добавить**.
1. В пункте **Параметры** выберите **Manual** (Вручную).
1. В качестве **имени**введите **рестапипассворд**.
    Префикс *B2C_1A_* может быть добавлен автоматически.
1. В поле **секрет** введите REST API пароль.
1. Для параметра **Использование ключа** задайте значение **Шифрование**.
1. Щелкните **Создать**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Настройка REST API технического профиля для использования обычной проверки подлинности HTTP

После создания необходимых ключей настройте метаданные технического профиля REST API, чтобы они ссылались на учетные данные.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Найдите REST API технический профиль. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>` .
1. Измените значение *AuthenticationType* на `Basic`.
1. Измените значение *алловинсекуреаусинпродуктион* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Ниже приведен пример технического профиля RESTFUL, настроенного с обычной проверкой подлинности HTTP.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Проверка подлинности сертификата клиента HTTPS

Проверка подлинности с помощью сертификата клиента — это взаимная проверка подлинности на основе сертификата, где клиент, Azure AD B2C, предоставляет серверу свой сертификат клиента для подтверждения его личности. Это происходит как часть подтверждения SSL. Только службы, имеющие соответствующие сертификаты, например Azure AD B2C, могут получить доступ к службе REST API. Сертификат клиента — это цифровой сертификат X. 509. В рабочих средах она должна быть подписана центром сертификации.

### <a name="prepare-a-self-signed-certificate-optional"></a>Подготовка самозаверяющего сертификата (необязательно)

Для нерабочих сред, если у вас еще нет сертификата, можно использовать самозаверяющий сертификат. В Windows для создания сертификата можно использовать командлет [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell.

1. Выполните эту команду PowerShell, чтобы создать самозаверяющий сертификат. Измените `-Subject` аргумент соответствующим образом для приложения и Azure AD B2C имя клиента. Можно также настроить `-NotAfter` дату для указания другого срока действия сертификата.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Откройте**Current User** > окно **Управление сертификатами** > пользователей**Личные** > **Сертификаты** > *yourappname.yourtenant.onmicrosoft.com*.
1. Выберите действие сертификат > **действия** > **все задачи** > **Экспорт**.
1. Нажмите кнопку **Да** > **Далее** > **Да, экспортировать закрытый ключ** > **Далее**.
1. Примите значения по умолчанию для **формата файла экспорта**.
1. Укажите пароль для сертификата.

### <a name="add-a-client-certificate-policy-key"></a>Добавление ключа политики сертификатов клиента

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** , а затем выберите Azure AD B2C каталог.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики**и нажмите кнопку **Добавить**.
1. В поле **Параметры** выберите **Отправить**.
1. В поле **имя** введите **рестапиклиентцертификате**.
    Префикс *B2C_1A_* добавляется автоматически.
1. В поле **Отправка файлов** выберите PFX-файл сертификата с закрытым ключом.
1. В поле **Пароль** введите пароль сертификата.
1. Щелкните **Создать**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Настройка REST API технического профиля для использования проверки подлинности с помощью сертификата клиента

После создания нужного ключа настройте метаданные технического профиля REST API, чтобы они ссылались на сертификат клиента.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Найдите REST API технический профиль. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>` .
1. Измените значение *AuthenticationType* на `ClientCertificate`.
1. Измените значение *алловинсекуреаусинпродуктион* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Ниже приведен пример технического профиля RESTFUL, настроенного с использованием сертификата клиента HTTP.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Проверка подлинности носителя OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Проверка подлинности токена носителя определяется в [инфраструктуре авторизации OAuth 2.0: использование токена носителя (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). При проверке подлинности токена носителя Azure AD B2C отправляет HTTP-запрос с маркером в заголовке авторизации.

```http
Authorization: Bearer <token>
```

Токен носителя является непрозрачной строкой. Это может быть маркер доступа JWT или любая строка, в которой REST API ждет отправки Azure AD B2C в заголовок авторизации. Azure AD B2C поддерживает следующие типы:

- **Токен носителя**. Чтобы иметь возможность отправить токен носителя в технический профиль RESTful, политика должна сначала получить маркер носителя, а затем использовать его в техническом профиле RESTFUL.  
- **Статический токен носителя**. Используйте этот подход, когда REST API выдает долгосрочный маркер доступа. Чтобы использовать статический маркер носителя, создайте ключ политики и создайте ссылку из технического профиля RESTFUL для ключа политики. 


## <a name="using-oauth2-bearer"></a>Использование носителя OAuth2  

В следующих шагах показано, как использовать учетные данные клиента для получения токена носителя и передачи его в заголовок Authorization для вызовов REST API.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Определение утверждения для хранения токена носителя

Утверждение предоставляет временное хранилище данных во время выполнения политики Azure AD B2C. [Схема утверждений](claimsschema.md) — это место, где объявляются утверждения. Маркер доступа должен храниться в утверждении для последующего использования. 

1. Откройте файл расширений политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Нахождение элемента [ClaimsSchema](claimsschema.md) . Если такой элемент не существует, добавьте его.
1. Добавьте следующие утверждения в элемент **ClaimsSchema** .  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Получение маркера доступа 

Маркер доступа можно получить одним из нескольких способов: путем его получения [из федеративного поставщика удостоверений](idp-pass-through-custom.md)путем вызова REST API, возвращающего маркер доступа, с помощью [потока ропк](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)или с помощью [потока учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).  

В следующем примере используется REST API технический профиль для выполнения запроса к конечной точке маркера Azure AD с использованием учетных данных клиента, переданных в качестве обычной проверки подлинности HTTP. Чтобы настроить это в Azure AD, см. статью [платформа идентификации Майкрософт и поток учетных данных клиента OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Может потребоваться изменить его для взаимодействия с поставщиком удостоверений. 

Для ServiceUrl замените имя клиента на имя своего клиента Azure AD. Все доступные варианты см. в справочнике по [техническим профилям RESTful](restful-technical-profile.md) .

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Изменение технического профиля RESTFUL для использования проверки подлинности токена носителя

Для поддержки проверки подлинности токена носителя в пользовательской политике измените REST API технический профиль следующим образом:

1. В рабочей папке откройте файл политики расширения *TrustFrameworkExtensions.xml*.
1. Найдите узел `<TechnicalProfile>`, содержащий `Id="REST-API-SignUp"`.
1. Найдите элемент `<Metadata>` .
1. Измените *AuthenticationType* на *носитель*, как показано ниже.
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Измените или добавьте *усеклаимасбеарертокен* в *беарертокен*, как показано ниже. *Беарертокен* — это имя утверждения, из которого будет получен токен носителя (выходное утверждение из `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Убедитесь, что вы добавили в качестве входящего утверждения следующее утверждение:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

После добавления приведенных выше фрагментов технический профиль должен выглядеть как следующий XML-код:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Использование статического носителя OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Добавление ключа политики токена носителя OAuth2

Создайте ключ политики для хранения значения токена носителя.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** , а затем выберите Azure AD B2C каталог.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики**и нажмите кнопку **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `RestApiBearerToken`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите ранее записанный секрет клиента.
1. Для параметра **Использование ключа** выберите `Encryption`.
1. Щелкните **Создать**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Настройка REST API технического профиля для использования ключа политики токена носителя

После создания нужного ключа настройте метаданные технического профиля REST API, чтобы они ссылались на токен носителя.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Найдите REST API технический профиль. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>` .
1. Измените значение *AuthenticationType* на `Bearer`.
1. Измените значение *алловинсекуреаусинпродуктион* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Ниже приведен пример технического профиля RESTFUL, настроенного с проверкой подлинности токена носителя:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения об элементе [технического профиля RESTful](restful-technical-profile.md) см. в справочнике по инфраструктура процедур идентификации. 

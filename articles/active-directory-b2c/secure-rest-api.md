---
title: Защищайте услугу отдыха в Azure AD B2C
titleSuffix: Azure AD B2C
description: Защищайте пользовательские обмены REST API в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396853"
---
# <a name="secure-your-restful-services"></a>Обезоверьтейте свои услуги RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

При интеграции REST API в путешествие пользователя Azure AD B2C необходимо защитить конечную точку REST API с помощью аутентификации. Это гарантирует, что только службы, обладающие надлежащими учетными данными, такие как Azure AD B2C, могут совершать звонки в конечную точку REST API.

Узнайте, как интегрировать API REST в путешествие пользователя Azure AD B2C в [подтвержденном входе пользователя](custom-policy-rest-api-claims-validation.md) и [добавьте обмен требованиями REST API в статьи пользовательских политик.](custom-policy-rest-api-claims-exchange.md)

В этой статье будет рассмотрен способ обезопажности вашего REST API с помощью базового сертификата HTTP, сертификата клиента или аутентификации OAuth2. 

## <a name="prerequisites"></a>Предварительные требования

Выполнить шаги в одном из следующих руководств «Как»

- [Интеграция rest API утверждает обмены в вашем путешествии пользователя Azure AD B2C для проверки пользовательского ввода.](custom-policy-rest-api-claims-validation.md)
- [Добавление обменов заявок REST API в пользовательские политики](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Базовая аутентификация HTTP

Базовая аутентификация HTTP определена в [RFC 2617](https://tools.ietf.org/html/rfc2617). Базовая аутентификация работает следующим образом: Azure AD B2C отправляет запрос HTTP с учетными данными клиента в заголовке авторизации. Учетные данные отформатированы как базовая строка "имя:пароль".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Добавление имени пользователя REST API и ключей политики паролей

Чтобы настроить технический профиль REST API с помощью базовой аутентификации HTTP, создайте следующие криптографические ключи для хранения имени пользователя и пароля:

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики,** а затем выберите **Добавить**.
1. В пункте **Параметры** выберите **Manual** (Вручную).
1. Для **названия**, типа **RestapiUsername**.
    Префикс *B2C_1A_* может быть добавлен автоматически.
1. В **secret** box введите имя пользователя REST API.
1. Для параметра **Использование ключа** задайте значение **Шифрование**.
1. Нажмите кнопку **создания**.
1. Выберите **ключи политики** еще раз.
1. Выберите **Добавить**.
1. В пункте **Параметры** выберите **Manual** (Вручную).
1. Для **имени**, типа **RestApiPassword**.
    Префикс *B2C_1A_* может быть добавлен автоматически.
1. В **secret** box введите пароль REST API.
1. Для параметра **Использование ключа** задайте значение **Шифрование**.
1. Нажмите кнопку **создания**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Нанастройка технического профиля REST API для использования базовой аутентификации HTTP

После создания необходимых ключей настроьте метаданные технического профиля REST API для ссылки на учетные данные.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Поиск технического профиля REST API. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>`.
1. Измените *тип проверки подлинности* на `Basic`.
1. Измените *AllowInsecureAuthInProduction* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Ниже приводится пример технического профиля RESTful, настроенного с помощью базовой аутентификации HTTP:

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

## <a name="https-client-certificate-authentication"></a>Аутентификация сертификата клиента HTTPS

Аутентификация сертификата клиента — это взаимная проверка подлинности на основе сертификата, когда клиент, Azure AD B2C, предоставляет сертификат клиента серверу, чтобы доказать свою личность. Это происходит как часть рукопожатия SSL. Доступ к службе REST API могут получить только службы, которые имеют соответствующие сертификаты, такие как Azure AD B2C. Сертификат клиента является цифровым сертификатом X.509. В производственных средах он должен быть подписан сертификационным органом.

### <a name="prepare-a-self-signed-certificate-optional"></a>Подготовка сертификата,подписанного самостоятельно (необязательно)

Для непроизводственных сред, если у вас еще нет сертификата, вы можете использовать сертификат, подписанный самостоятельно. В Windows для создания сертификата можно использовать смдлет PowerShell [New-SelfSignedCertificate.](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)

1. Выполните эту команду PowerShell для создания сертификата, подписанного самостоятельно. Измените `-Subject` аргумент, подходящий для приложения и имени клиента Azure AD B2C. Можно также настроить `-NotAfter` дату, чтобы указать другой срок действия сертификата.
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
1. Открытое **управление пользовательскими сертификатами** > **Текущие** > **личные** > **сертификаты** > пользователя*yourappname.yourtenant.onmicrosoft.com*.
1. Выберите сертификат > **Action** > **All Tasks** > **Export.**
1. Выберите **Да** > **Далее** > **Да, экспортировать частный ключ** > **Далее**.
1. Примите по умолчанию для **формата экспортных файлов.**
1. Предоставьте пароль для сертификата.

### <a name="add-a-client-certificate-policy-key"></a>Добавление ключа политики сертификата клиента

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики,** а затем выберите **Добавить**.
1. В поле **Параметры** выберите **Отправить**.
1. В поле **Имя,** введите **RestApiClientCertificate**.
    Префикс *B2C_1A_* добавляется автоматически.
1. В поле **Отправка файлов** выберите PFX-файл сертификата с закрытым ключом.
1. В поле **Пароль** введите пароль сертификата.
1. Нажмите кнопку **создания**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Нанастройка технического профиля REST API для использования аутентификации сертификата клиента

После создания необходимого ключа настроьте метаданные технического профиля REST API для ссылки на сертификат клиента.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Поиск технического профиля REST API. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>`.
1. Измените *тип проверки подлинности* на `ClientCertificate`.
1. Измените *AllowInsecureAuthInProduction* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Ниже приводится пример технического профиля RESTful, настроенного с сертификатом клиента HTTP:

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

## <a name="oauth2-bearer-authentication"></a>Аутентификация предъявителя OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Проверка подлинности маркеров предъявителя определена в [рамочной системе авторизации OAuth2.0: Использование токенов для предъявителя (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) В аутенке маркера предъявителя Azure AD B2C отправляет запрос HTTP с токеном в заголовке авторизации.

```http
Authorization: Bearer <token>
```

Токен носителя — это непрозрачная строка. Это может быть токен доступа JWT или любая строка, которую REST API ожидает, что Azure AD B2C отправит заголовок авторизации. Azure AD B2C поддерживает следующие типы:

- **Токен носителя**. Чтобы иметь возможность отправить токен предъявителя в техническом профиле Restful, ваша политика должна сначала приобрести токен носителя, а затем использовать его в техническом профиле RESTful.  
- **Статический маркер носителя**. Используйте этот подход, когда ваш REST API выдает токен долгосрочного доступа. Чтобы использовать маркер статического носителя, создайте ключ политики и сделайте ссылку из технического профиля RESTful на ваш ключ политики. 


## <a name="using-oauth2-bearer"></a>Использование OAuth2 Носителя  

Следующие шаги демонстрируют, как использовать учетные данные клиента для получения маркера носителя и передачи его в заголовок авторизации вызовов REST API.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Определить претензию для хранения маркера носителя

Претензия предусматривает временное хранение данных во время выполнения политики Azure AD B2C. [Схема претензий](claimsschema.md) — это место, где вы объявляете свои претензии. Токен доступа должен храниться в претензии, которая будет использоваться позже. 

1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema.](claimsschema.md) Если такой элемент не существует, добавьте его.
1. Добавьте городской носитель Token в элемент **ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Приобретение токена доступа 

Вы можете получить токен доступа одним из нескольких способов: получив его [от поставщика federated identity,](idp-pass-through-custom.md)позвонив в REST API, который возвращает токен доступа, используя [поток ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)или с помощью [потока учетных данных клиента.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)  

В следующем примере используется технический профиль REST API для запроса в конечную точку маркера Azure AD с использованием учетных данных клиента, передаваемых как базовая аутентификация HTTP. Чтобы настроить это в Azure AD, см. [платформу идентификации Майкрософт и поток учетных данных клиентов OAuth 2.0.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) Возможно, вам придется изменить это, чтобы взаимодействовать с вашим поставщиком идентификационных данных. 

Для ServiceUrl замените имя арендатора именем арендатора Azure AD. Ознакомьтесь со ссылкой на [технический профиль RESTful](restful-technical-profile.md) для всех доступных вариантов.

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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Изменение технического профиля REST для использования проверки подлинности маркеров на предъявителя

Чтобы поддержать проверку подлинности маркеров на предъявителя в пользовательской политике, измените технический профиль REST API со следующим:

1. В рабочей папке откройте файл политики расширения *TrustFrameworkExtensions.xml*.
1. Найдите узел `<TechnicalProfile>`, содержащий `Id="REST-API-SignUp"`.
1. Найдите элемент `<Metadata>`.
1. Измените *тип аутентификации* на *Bearer:*
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Изменение или добавление *UseClaimAsBearerToken* на *предъявительToken*, следующим образом. *НосительToken* это название претензии, что маркер предъявителя будет извлечен из (выход претензии от `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Убедитесь, что вы добавляете претензию, используемую выше, в качестве входиного требования:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

После добавления вышеуказанных фрагментов технический профиль должен выглядеть следующим кодом XML:

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
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Использование статического носителя OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Добавление ключа политики маркера на предъявителя OAuth2

Создайте ключ политики для хранения значения маркера на предъявителя.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. На странице "Обзор" выберите **Identity Experience Framework**.
1. Выберите **ключи политики,** а затем выберите **Добавить**.
1. Для пункта **Параметры** выберите `Manual`.
1. Введите **имя** ключа политики. Например, `RestApiBearerToken`. Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
1. В поле **Секрет** введите ранее записанный секрет клиента.
1. Для параметра **Использование ключа** выберите `Encryption`.
1. Нажмите кнопку **создания**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Налаживайте технический профиль REST API, чтобы использовать ключ политики маркера предъявителя

После создания необходимого ключа настроьте метаданные технического профиля REST API для ссылки на токен носителя.

1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).
1. Поиск технического профиля REST API. Например, `REST-ValidateProfile` или `REST-GetProfile`.
1. Найдите элемент `<Metadata>`.
1. Измените *тип проверки подлинности* на `Bearer`.
1. Измените *AllowInsecureAuthInProduction* на `false`.
1. Сразу после завершения элемента `</Metadata>` добавьте следующий фрагмент XML-кода:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Ниже приводится пример технического профиля RESTful, настроенного с аутентификацией маркеров на предъявителя:

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

## <a name="next-steps"></a>Следующие шаги

- Подробнее о элементе [технического профиля Restful](restful-technical-profile.md) читайте в справке IEF. 

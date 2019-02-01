---
title: Настройка потока учетных данных пароля владельца ресурса в Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настроить поток учетных данных пароля владельца ресурса в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7dffa1480be73f1dbf5e99d11fd8d33eb2ab9038
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196418"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Настройка потока учетных данных пароля владельца ресурса в Azure Active Directory B2C с помощью пользовательской политики

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В Azure Active Directory (Azure AD) B2C поток учетных данных пароля владельца ресурса (ROPC) — это процесс стандартной проверки подлинности OAuth. В этом потоке приложение, также известное как проверяющая сторона, обменивает действительные учетные данные на маркеры проверки подлинности. Учетные данные включают идентификатор пользователя и пароль. Возвращается маркер идентификации, маркер доступа и маркер обновления. 

В потоке ROPC поддерживаются следующие параметры.

- **Собственный клиент**. Взаимодействие с пользователем во время аутентификации происходит, когда код выполняется на устройстве на стороне пользователя.
- **Поток общедоступного клиента**. При вызове API отправляются только учетные данные, полученные приложением. Учетные данные приложения не отправляются.
- **Добавление новых утверждений**. Содержимое токена идентификатора можно изменить для добавления новых утверждений. 

Следующие потоки не поддерживаются.

- **Сервер — сервер**. Системе защиты идентификации требуются надежные IP-адреса, полученные от вызывающего объекта (собственного клиента) в рамках взаимодействия. При вызове API на стороне сервера используется только IP-адрес сервера. После большого количества неудачных попыток входа система защиты идентификации может рассматривать повторяющийся IP-адрес как злоумышленника.
- **Одностраничное приложение** — клиентское приложение, написанное главным образом на JavaScript. Часто приложение создается с помощью таких платформ, как AngularJS, Ember.js или Durandal.
- **Последовательность конфиденциального клиента**. Проверка идентификатора клиента приложения выполняется, но секрет приложения не проверяется.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Регистрация приложения

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**. 
4. Щелкните **Приложения**, а затем выберите **Добавить**.
5. Укажите имя приложения, например *ROPC_Auth_app*.
6. Выберите значение **Нет** для параметра **Веб-приложение или веб-интерфейс API** и значение **Да** для параметра **Собственный клиент**.
7. Не меняя остальные значения, нажмите кнопку **Создать**.
8. Выберите новое приложение и запишите идентификатор приложения для дальнейшего использования.

##  <a name="create-a-resource-owner-policy"></a>Создание политики владельца ресурса

1. Откройте файл *TrustFrameworkExtensions.xml*.
2. Если он еще не существует, добавьте элемент **ClaimsSchema** и его дочерние элементы в качестве первого элемента в элементе **BuildingBlocks**.

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. После **ClaimsSchema** добавьте элемент **ClaimsTransformations** и его дочерние элементы в элемент **BuildingBlocks**.

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Найдите элемент **ClaimsProvider**, у которого параметр **DisplayName** имеет значение `Local Account SignIn`, и добавьте следующий технической профиль.

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Замените параметр **DefaultValue** для **client_id** и **resource_id** идентификатором приложения ProxyIdentityExperienceFramework, созданного в учебнике о предварительных требованиях.

5. Добавьте следующие элементы **ClaimsProvider** с техническими профилями в элемент **ClaimsProviders**.

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>    
    ```

6. Добавьте элемент **UserJourneys** и его дочерние элементы в элемент **TrustFrameworkPolicy**.

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
        <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
8. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
9. Щелкните **Отправить**.

## <a name="create-a-relying-party-file"></a>Создание файла проверяющей стороны

Теперь обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию файла *SignUpOrSignIn.xml* в рабочем каталоге и переименуйте его в *ROPC_Auth.xml*.
2. Откройте новый файл и обновите значение атрибута **PolicyId** для **TrustFrameworkPolicy**, указав уникальное значение. Идентификатор политики — это имя вашей политики. Например, **B2C_1A_ROPC_Auth**.
3. Измените значение атрибута **ReferenceId** в **DefaultUserJourney** на `ResourceOwnerPasswordCredentials`.
4. Измените элемент **OutputClaims**, чтобы он содержал только следующие утверждения.
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. На странице **Пользовательские политики** в клиенте Azure AD B2C выберите **Отправить политику**.
6. Включите функцию **Перезаписать политику, если она уже существует**, а затем найдите и выберите файл *TrustFrameworkExtensions.xml*.
7. Щелкните **Отправить**.

## <a name="test-the-policy"></a>Проверка политики

С помощью предпочитаемого приложения по разработке API создайте вызов API и просмотрите ответ, чтобы отладить политику. Создайте вызов, как в этом примере, со следующими сведениями в качестве текста запроса POST.

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Замените `your-tenant-name` именем вашего клиента Azure AD B2C.
- Замените `B2C_1A_ROPC_Auth` полным именем политики учетных данных пароля владельца ресурса.

| Ключ | Значение |
| --- | ----- |
| Имя пользователя | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Замените `user-account` на имя учетной записи пользователя в клиенте.
- Замените `password1` паролем к учетной записи пользователя.
- Замените `application-id` на идентификатор приложения из регистрации *ROPC_Auth_app*. 
- *Offline_access* является необязательным, если вы хотите получить токен обновления.

Фактический запрос POST выглядит так.

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Успешный ответ с автономным доступом выглядит так:

```JSON
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..." 
} 
```

## <a name="redeem-a-refresh-token"></a>Погашение токена обновления

Создайте вызов POST, аналогичный приведенному здесь. Используйте приведенные в таблице ниже сведения в качестве текста запроса.

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Замените `your-tenant-name` именем вашего клиента Azure AD B2C.
- Замените `B2C_1A_ROPC_Auth` полным именем политики учетных данных пароля владельца ресурса.

| Ключ | Значение |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Замените `application-id` на идентификатор приложения из регистрации *ROPC_Auth_app*.
- Замените `refresh-token` на **refresh_token**, который был отправлен в предыдущем ответе. 

Успешный ответ выглядит примерно так:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Использование собственного пакета SDK или App-Auth

Azure AD B2C соответствует стандартам OAuth 2.0 для учетных данных пароля владельца ресурса общедоступного клиента и должен быть совместим с большинством клиентских пакетов SDK. См. последние [рекомендации по реализации собственного пакета SDK для приложений OAuth 2.0 и OpenID Connect](https://appauth.io/).

## <a name="next-steps"></a>Дополнительная информация

- См. полный пример такого сценария в [начальном пакете настраиваемой политики Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Дополнительные сведения о маркерах, которые используются Azure Active Directory B2C, см. в [справочнике по маркерам](active-directory-b2c-reference-tokens.md).



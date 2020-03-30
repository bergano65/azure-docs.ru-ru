---
title: REST API требует обмены как проверку
titleSuffix: Azure AD B2C
description: Пошаговая помощь для создания пользовательского путешествия Azure AD B2C, которое взаимодействует с службами RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330817"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Пошаговая прогулка: Интеграция REST API утверждает обмены в вашем путешествии пользователя Azure AD B2C для проверки пользовательского ввода

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Рамочная система identity Experience (IEF), лежащая в основе Azure Active Directory B2C (Azure AD B2C), позволяет разработчикам идентификационных данных интегрировать взаимодействие с API RESTful в пользовательском путешествии.  В конце этого пошагового листа вы сможете создать путешествие пользователя Azure AD B2C, которое взаимодействует с [службами RESTful](custom-policy-rest-api-intro.md) для проверки пользовательского ввода.

В этом случае мы добавим возможность для пользователей ввести номер лояльности на страницу регистрации Azure AD B2C. Мы проверяем, отображается ли эта комбинация электронной почты и номера лояльности в промо-код, отправив эти данные на API REST. Если REST API найдет рекламный код для этого пользователя, он будет возвращен в Azure AD B2C. Наконец, промо-код будет вставлен в требования маркера для потребления приложения.

Взаимодействие можно также реализовать на этапе оркестрации. Это подходит, когда REST API не будет проверять данные на экране и всегда возвращать претензии. Дополнительные сведения см. в статье [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.
- Узнайте, как [интегрировать обмены требованиями REST API в пользовательской политике Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Подготовьте конечную точку REST API

Для этого пошагового письма необходимо иметь REST API, который проверяет, зарегистрирован ли адрес электронной почты в вашей системе с идентификатором лояльности. В случае регистрации REST API должен вернуть код продвижения регистрации, который клиент может использовать для покупки товаров в вашем приложении. В противном случае REST API должен вернуть сообщение об ошибке HTTP 409: «Идентификатор лояльности »не связан с адресом электронной почты».

Следующий код JSON иллюстрирует данные, которые Azure AD B2C отправит на конечную точку REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Как только ваш REST API проверяет данные, он должен вернуть HTTP 200 (Ok) со следующими данными JSON:

```json
{
    "promoCode": "24534"
}
```

Если проверка не удалась, API REST должен вернуть HTTP `userMessage` 409 (Конфликт) с элементом JSON. IEF ожидает, `userMessage` что претензия REST API возвращается. Эта претензия будет представлена в качестве строки для пользователя, если проверка не удается.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Настройка конечной точки REST API выходит за рамки данной статьи. Мы создали образец [функций Azure.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Вы можете получить доступ к полному коду функции Azure на [GitHub.](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)

## <a name="define-claims"></a>Определение претензий

Претензия предусматривает временное хранение данных во время выполнения политики Azure AD B2C. Вы можете декларировать претензии в разделе [схемы претензий.](claimsschema.md) 

1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Найдите элемент [ClaimsSchema.](claimsschema.md) Если такой элемент не существует, добавьте его.
1. Добавьте следующие претензии к элементу **ClaimsSchema.**  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Настройка технического профиля API RESTful 

[Restful технический профиль](restful-technical-profile.md) обеспечивает поддержку для interfacing к вашему собственному обслуживанию RESTful. Azure AD B2C отправляет данные в службу `InputClaims` RESTful в `OutputClaims` коллекции и получает данные обратно в коллекцию. Найти элемент **ClaimsProviders** и добавить нового поставщика претензий следующим образом:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

В этом примере будет отправлено `userLanguage` в `lang` службу REST как в полезной нагрузке JSON. Значение претензии `userLanguage` содержит текущий идентификатор языка пользователя. Для получения дополнительной [claim resolver](claim-resolver-overview.md)информации см.

Комментарии `AuthenticationType` выше `AllowInsecureAuthInProduction` и указать изменения, которые вы должны сделать при переходе к производственной среде. Чтобы узнать, как обеспечить ваши RESTful API для производства, [см.](secure-rest-api.md)

## <a name="validate-the-user-input"></a>Проверка ввода пользователя

Чтобы получить номер лояльности пользователя во время регистрации, необходимо разрешить пользователю ввести эти данные на экране. Добавьте требование о выходе **loyaltyId** на страницу регистрации, добавив ее в `OutputClaims` элемент существующего раздела технического профиля регистрации. Укажите весь список выходных требований для контроля за заказом, на экране представлены претензии.  

Добавьте ссылку на технический профиль проверки в `REST-ValidateProfile`технический профиль регистрации, который вызывает . Новый технический профиль проверки будет добавлен `<ValidationTechnicalProfiles>` в верхнюю часть коллекции, определяемую в базовой политике. Это поведение означает, что только после успешной проверки Azure AD B2C переходит к созданию учетной записи в каталоге.   

1. Найдите элемент **ClaimsProviders**. Добавить нового поставщика претензий следующим образом:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Включить претензию в токен 

Чтобы вернуть претензию промо-кода обратно в приложение опирающейся стороны, добавьте претензию вывода в <em> `SocialAndLocalAccounts/` </em> файл. Претензия на выходе позволит добавить претензию в токен после успешного путешествия пользователя и будет отправлена в приложение. Измените элемент технического профиля в разделе `promoCode` опирающейся стороны, чтобы добавить в качестве претензии вывода.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога в** верхнем меню и выбрав каталог, содержащий арендатор Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **загрузку пользовательской политики,** а затем загрузите измененные файлы политики: *TrustFrameworkExtensions.xml*и *SignUpOrSignin.xml.* 
1. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
1. Вы сможете зарегистрироваться, используя адрес электронной почты.
1. Нажмите на ссылку **Регистрация.**
1. В **Идентификаторе лояльности Your,** введите 1234 и нажмите **Продолжить**. На этом этапе вы должны получить сообщение об ошибке проверки.
1. Изменение на другое значение и нажмите **Продолжить**.
1. Токен, отправленный обратно в ваше приложение, включает утверждение `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как обезопасить свои AA, смотрите следующие статьи:

- [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md)
- [Защитите свой RESTful API](secure-rest-api.md)
- [Справка: Технический профиль RESTful](restful-technical-profile.md)

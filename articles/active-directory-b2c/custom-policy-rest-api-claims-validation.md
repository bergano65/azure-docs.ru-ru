---
title: REST API обмен утверждениями в качестве проверки
titleSuffix: Azure AD B2C
description: Пошаговое руководство по созданию Azure AD B2C пути взаимодействия пользователя, взаимодействующего со службами RESTFUL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330817"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Пошаговое руководство. интеграция обмена REST APIми утверждениями в Azure AD B2C пути взаимодействия пользователя для проверки вводимых пользователем данных

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Инфраструктура процедур идентификации (инфраструктура процедур идентификации), которая подкрепляет Azure Active Directory B2C (Azure AD B2C), позволяет разработчикам удостоверений интегрировать взаимодействие с API RESTFUL в пути взаимодействия пользователя.  В конце этого пошагового руководства вы сможете создать Azure AD B2C пути взаимодействия пользователя, который взаимодействует с [службами RESTful](custom-policy-rest-api-intro.md) для проверки вводимых пользователем данных.

В этом сценарии мы добавим возможность ввода номера лояльности для пользователей на странице регистрации Azure AD B2C. Мы пропроверяем, сопоставил ли это сочетание электронной почты и номера программы лояльности с рекламным кодом, отправив эти данные в REST API. Если REST API находит рекламный код для этого пользователя, он будет возвращен в Azure AD B2C. И, наконец, код рекламной акции будет вставлен в утверждения маркеров для использования приложением.

Взаимодействие можно также реализовать на этапе оркестрации. Это подходит, если REST API не будет проверять данные на экране и всегда возвращать утверждения. Дополнительные сведения см. в статье [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.
- Узнайте, как [интегрировать обмен утверждениями REST API в настраиваемой политике Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Подготовка конечной точки REST API

В этом пошаговом руководстве у вас должен быть REST API, проверяющий, зарегистрирован ли адрес электронной почты в серверной системе с ИДЕНТИФИКАТОРом лояльности. В случае регистрации REST API должен вернуть код рекламной акции, который клиент может использовать для покупки товаров в приложении. В противном случае REST API должен вернуть сообщение об ошибке HTTP 409: "идентификатор лояльности" {лояльность ID} "не связан с адресом электронной почты" {Email} ".".

В следующем коде JSON показано, Azure AD B2C данные будут отправлены в конечную точку REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

После того, как REST API проверит данные, он должен вернуть HTTP 200 (ОК) со следующими данными JSON:

```json
{
    "promoCode": "24534"
}
```

Если проверка завершилась неудачей, REST API должен вернуть HTTP 409 (конфликт) с элементом `userMessage` JSON. ИНФРАСТРУКТУРА процедур идентификации ждет `userMessage` утверждения, возвращаемого REST API. Это утверждение будет представлено в виде строки пользователю в случае сбоя проверки.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Настройка конечной точки REST API выходит за рамки этой статьи. Мы создали пример [функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) . Полный код функции Azure можно получить на сайте [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Определение утверждений

Утверждение предоставляет временное хранилище данных во время выполнения политики Azure AD B2C. Утверждения можно объявлять в разделе [схемы утверждений](claimsschema.md) . 

1. Откройте файл расширений политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент [BuildingBlocks](buildingblocks.md). Если такой элемент не существует, добавьте его.
1. Нахождение элемента [ClaimsSchema](claimsschema.md) . Если такой элемент не существует, добавьте его.
1. Добавьте следующие утверждения в элемент **ClaimsSchema** .  

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

## <a name="configure-the-restful-api-technical-profile"></a>Настройка технического профиля API RESTFUL 

[Технический профиль RESTful](restful-technical-profile.md) обеспечивает поддержку взаимодействия со службой RESTful. Azure AD B2C отправляет данные в службу RESTFUL в `InputClaims` коллекции и получает данные обратно в `OutputClaims` коллекции. Найдите элемент **клаимспровидерс** и добавьте новый поставщик утверждений следующим образом:

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

В этом примере объект `userLanguage` будет отправлен в службу RESTful, как `lang` в полезных данных JSON. Значение `userLanguage` утверждения содержит текущий идентификатор языка пользователя. Дополнительные сведения см. в разделе [сопоставитель утверждений](claim-resolver-overview.md).

Комментарии выше `AuthenticationType` и `AllowInsecureAuthInProduction` указывают изменения, которые необходимо внести при переходе в рабочую среду. Чтобы узнать, как защитить интерфейсы API RESTFUL для рабочей среды, см. раздел [Защита API RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Проверка вводимых пользователем данных

Чтобы получить номер лояльности пользователя во время регистрации, необходимо разрешить пользователю вводить эти данные на экране. Добавьте исходящие утверждения **лойалтид** на страницу регистрации, добавив ее в существующий `OutputClaims` элемент раздела технического профиля регистрации. Укажите полный список исходящих утверждений для управления порядком, в котором утверждения представлены на экране.  

Добавьте ссылку на технический профиль проверки в технический профиль регистрации, который вызывает `REST-ValidateProfile`. Новый технический профиль проверки будет добавлен в верхнюю часть `<ValidationTechnicalProfiles>` коллекции, определенной в базовой политике. Такое поведение означает, что только после успешной проверки Azure AD B2C переходит на создание учетной записи в каталоге.   

1. Найдите элемент **ClaimsProviders**. Добавьте новый поставщик утверждений следующим образом:

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

## <a name="include-a-claim-in-the-token"></a>Включить утверждение в маркер 

Чтобы вернуть утверждение кода Акционная обратно в приложение проверяющей стороны, добавьте выходное утверждение в <em> `SocialAndLocalAccounts/` </em> файл. Выходное утверждение позволит добавить утверждение в маркер после успешного пути взаимодействия пользователя и будет отправлено приложению. Измените элемент технического профиля в разделе проверяющей стороны, чтобы добавить в `promoCode` качестве выходного утверждения.
 
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
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **Отправить настраиваемую политику**и отправьте измененные файлы политики: *TrustFrameworkExtensions. XML*и *SignUpOrSignin. XML*. 
1. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
1. Вы сможете зарегистрироваться, используя адрес электронной почты.
1. Щелкните ссылку **Зарегистрировать сейчас** .
1. В поле **идентификатор программы лояльности**введите 1234 и нажмите кнопку **продолжить**. На этом этапе следует получить сообщение об ошибке проверки.
1. Измените значение на другое и нажмите кнопку **продолжить**.
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

Дополнительные сведения о защите API см. в следующих статьях:

- [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md)
- [Защита API RESTFUL](secure-rest-api.md)
- [Справочник: технический профиль RESTFUL](restful-technical-profile.md)

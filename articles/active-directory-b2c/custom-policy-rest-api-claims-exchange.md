---
title: Обмен утверждениями REST API — Azure Active Directory B2C
description: Добавление REST API заявок на обмен утверждениями в пользовательские политики в Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330721"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Пошаговое руководство. Добавление REST API обмена утверждениями в пользовательские политики в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) позволяет разработчикам удостоверений интегрировать взаимодействие с API RESTFUL в пути взаимодействия пользователя. В конце этого пошагового руководства вы сможете создать Azure AD B2C пути взаимодействия пользователя, который взаимодействует со [службами RESTful](custom-policy-rest-api-intro.md).

В этом сценарии мы дополнены данными маркера пользователя, интегрируя корпоративный бизнес-процесс. Во время регистрации или входа с локальной или федеративной учетной записью Azure AD B2C вызывает REST API, чтобы получить расширенные данные профиля пользователя из удаленного источника данных. В этом примере Azure AD B2C отправляет уникальный идентификатор пользователя, objectId. Затем REST API возвращает баланс учетной записи пользователя (случайное число). Используйте этот пример в качестве отправной точки для интеграции с вашей собственной системой CRM, маркетинговой базой данных или рабочим процессом любого бизнес-процесса.

Вы также можете разработать взаимодействие в качестве технического профиля проверки. Это подходит, когда REST API будет проверять данные на экране и возвращать утверждения. Дополнительные сведения см. [в разделе Пошаговое руководство. интеграция REST APIных обменов утверждениями в Azure AD B2C пути взаимодействия пользователя для проверки вводимых пользователем данных](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.
- Узнайте, как [интегрировать обмен утверждениями REST API в настраиваемой политике Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Подготовка конечной точки REST API

В этом пошаговом руководстве у вас должна быть REST API, которая проверяет, зарегистрирован ли Azure AD B2C objectId пользователя в серверной системе. При регистрации REST API возвращает баланс учетной записи пользователя. В противном случае REST API регистрирует новую учетную запись в каталоге и возвращает начальный баланс `50.00`.

В следующем коде JSON показано, Azure AD B2C данные будут отправлены в конечную точку REST API. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

После того, как REST API проверит данные, он должен вернуть HTTP 200 (ОК) со следующими данными JSON:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Настройка технического профиля API RESTFUL 

[Технический профиль RESTful](restful-technical-profile.md) обеспечивает поддержку взаимодействия с собственной службой RESTful. Azure AD B2C отправляет данные в службу RESTFUL в `InputClaims` коллекции и получает данные обратно в `OutputClaims` коллекции. Найдите в **ClaimsProviders** <em>**`TrustFrameworkExtensions.xml`**</em> файле элемент клаимспровидерс и добавьте новый поставщик утверждений, как показано ниже.

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
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

В этом примере объект `userLanguage` будет отправлен в службу RESTful, как `lang` в полезных данных JSON. Значение `userLanguage` утверждения содержит текущий идентификатор языка пользователя. Дополнительные сведения см. в разделе [сопоставитель утверждений](claim-resolver-overview.md).

Комментарии выше `AuthenticationType` и `AllowInsecureAuthInProduction` указывают изменения, которые необходимо внести при переходе в рабочую среду. Чтобы узнать, как защитить интерфейсы API RESTFUL для рабочей среды, см. раздел [Защита API RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Добавление шага оркестрации

Пути взаимодействия [пользователя](userjourneys.md) указывают явное указание путей, через которые политика разрешает приложению проверяющей стороны получать нужные утверждения для пользователя. Путь взаимодействия пользователя представляется в виде последовательности оркестрации, которая должна быть соблюдена для успешного выполнения транзакции. Можно добавить или прочесть шаги оркестрации. В этом случае вы добавите новый этап оркестрации, который будет использоваться для дополнения сведений, предоставленных приложению после регистрации или входа пользователя через вызов REST API.

1. Откройте базовый файл политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент `<UserJourneys>`. Скопируйте весь элемент, а затем удалите его.
1. Откройте файл расширений политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. `<UserJourneys>` Вставьте в файл Extensions после закрытия `<ClaimsProviders>` элемента.
1. `<UserJourney Id="SignUpOrSignIn">`Выберите и добавьте следующий шаг оркестрации перед последним.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Выполните рефакторинг последнего шага оркестрации, изменив значение `Order` на `8`. Последние два шага оркестрации должны выглядеть следующим образом:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Повторите два последних шага для пути взаимодействия пользователя **ProfileEdit** и **PasswordReset** .


## <a name="include-a-claim-in-the-token"></a>Включить утверждение в маркер 

Чтобы вернуть `balance` заявку обратно в приложение проверяющей стороны, добавьте в <em> `SocialAndLocalAccounts/` </em> файл выходное утверждение. Добавление исходящего утверждения приведет к подаче заявки в маркер после успешного пути взаимодействия пользователя и будет отправлено приложению. Измените элемент технического профиля в разделе проверяющей стороны, чтобы добавить `balance` его в качестве выходного утверждения.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Повторите этот шаг для пути взаимодействия пользователя **ProfileEdit. XML**и **PasswordReset. XML** .

Сохраните измененные файлы: *TrustFrameworkBase. XML*и *TrustFrameworkExtensions. XML*, *SignUpOrSignin. XML*, *ProfileEdit. XML*и *PasswordReset. XML*. 

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **Отправить настраиваемую политику**, а затем отправьте измененные файлы политики: *TrustFrameworkBase. XML*и *TrustFrameworkExtensions. XML*, *SignUpOrSignin. XML*, *ProfileEdit. XML*и *PasswordReset. XML*. 
1. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
1. Вы можете зарегистрироваться, используя адрес электронной почты или учетную запись Facebook.
1. Токен, отправленный обратно в ваше приложение, включает утверждение `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Дальнейшие действия


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о защите API см. в следующих статьях:

- [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md)
- [Защита API RESTFUL](secure-rest-api.md)
- [Справочник: технический профиль RESTFUL](restful-technical-profile.md)

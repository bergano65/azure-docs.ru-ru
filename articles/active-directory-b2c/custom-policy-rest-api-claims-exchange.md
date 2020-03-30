---
title: REST API утверждает, обмены - Azure Активный каталог B2C
description: Добавьте обмен заявок REST API в пользовательские политики в Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330721"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Пошаговая прогулка: Добавление обменов требованиями REST API к пользовательским политикам в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) позволяет разработчикам идентификационных данных интегрировать взаимодействие с API RESTful в пользовательском путешествии. В конце этого пошагового листа вы сможете создать путешествие пользователя Azure AD B2C, которое взаимодействует с [службами RESTful.](custom-policy-rest-api-intro.md)

В этом случае мы обогащаем данные маркеров пользователя, интегрируясь с корпоративным рабочим процессом. Во время регистрации или регистрации в локальной или федеративной учетной записи Azure AD B2C вызывает aPI REST, чтобы получить данные расширенного профиля пользователя из удаленного источника данных. В этом примере Azure AD B2C отправляет уникальный идентификатор пользователя, objectId. Затем REST API возвращает баланс счета пользователя (случайное число). Используйте этот образец в качестве отправной точки для интеграции с вашей собственной crM-системой, базой данных маркетинга или любым рабочим процессом в бизнесе.

Можно также спроектировать взаимодействие в качестве технического профиля проверки. Это подходит, когда REST API будет проверять данные на экране и возвращать претензии. Для получения дополнительной информации [см. Walkthrough: Интеграция REST API утверждает обмены в вашем путешествии пользователя Azure AD B2C для проверки пользовательского ввода.](custom-policy-rest-api-claims-validation.md)

## <a name="prerequisites"></a>Предварительные требования

- Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.
- Узнайте, как [интегрировать обмены требованиями REST API в пользовательской политике Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Подготовьте конечную точку REST API

Для этого пошагового управления необходимо иметь aPI REST, который проверяет, зарегистрирован ли объект Azure AD B2C пользователя в вашей системе обратного конца. Если он зарегистрирован, ТО REST API возвращает остаток счета пользователя. В противном случае REST API регистрирует новый счет в `50.00`каталоге и возвращает исходный баланс.

Следующий код JSON иллюстрирует данные, которые Azure AD B2C отправит на конечную точку REST API. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Как только ваш REST API проверяет данные, он должен вернуть HTTP 200 (Ok) со следующими данными JSON:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Настройка технического профиля API RESTful 

[Restful технический профиль](restful-technical-profile.md) обеспечивает поддержку для взаимосвязи с вашим собственным сервисом RESTful. Azure AD B2C отправляет данные в службу `InputClaims` RESTful в `OutputClaims` коллекции и получает данные обратно в коллекцию. Найдите элемент **ClaimsProviders** в файле <em>**`TrustFrameworkExtensions.xml`**</em> и добавьте нового поставщика претензий следующим образом:

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

В этом примере будет отправлено `userLanguage` в `lang` службу REST как в полезной нагрузке JSON. Значение претензии `userLanguage` содержит текущий идентификатор языка пользователя. Для получения дополнительной [claim resolver](claim-resolver-overview.md)информации см.

Комментарии `AuthenticationType` выше `AllowInsecureAuthInProduction` и указать изменения, которые вы должны сделать при переходе к производственной среде. Чтобы узнать, как обеспечить ваши RESTful API для производства, [см.](secure-rest-api.md)

## <a name="add-an-orchestration-step"></a>Добавление шага оркестровки

[В поездках пользователей](userjourneys.md) указываются четкие пути, по которым политика позволяет приложению, полагающемуся на сторону, получить желаемые требования для пользователя. Путь взаимодействия пользователя представляется в виде последовательности оркестрации, которая должна быть соблюдена для успешного выполнения транзакции. Можно добавить или вычесть шаги оркестровки. В этом случае вы добавите новый шаг оркестровки, который используется для увеличения информации, предоставленной приложению после регистрации пользователя или регистрации через вызов REST API.

1. Откройте базовый файл политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент `<UserJourneys>`. Скопируйте весь элемент, а затем удалите его.
1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Вставьте `<UserJourneys>` файл расширений после закрытия элемента. `<ClaimsProviders>`
1. Найдите `<UserJourney Id="SignUpOrSignIn">`, и добавить следующий шаг оркестровки до последнего.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Рефакторинг последний шаг оркестровки, изменив `Order` к `8`. Ваши последние два шага оркестровки должны выглядеть следующим образом:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Повторите последние два шага для поездок пользователей **ProfileEdit** и **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Включить претензию в токен 

Чтобы вернуть `balance` претензию обратно в заявку на использование <em> `SocialAndLocalAccounts/` </em> заявителя, добавьте претензию вывода в файл. Добавление претензии вывода выдаст претензию в токен после успешного путешествия пользователя и будет отправлен в приложение. Изменение элемента технического профиля в разделе `balance` опирающейся стороны для добавления в качестве претензии вывода.
 
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

Повторите этот шаг для поездок пользователей **ProfileEdit.xml**и **PasswordReset.xml.**

Сохранить файлы, которые вы изменили: *TrustFrameworkBase.xml*, и *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, и *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога в** верхнем меню и выбрав каталог, содержащий арендатор Azure AD.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **загрузить пользовательскую политику,** а затем загрузить файлы политики, которые вы изменили: *TrustFrameworkBase.xml*, и *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, и *PasswordReset.xml*. 
1. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
1. Вы должны иметь возможность зарегистрироваться с помощью адреса электронной почты или учетной записи Facebook.
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

Чтобы узнать, как обезопасить свои AA, смотрите следующие статьи:

- [Пошаговое руководство. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как этап оркестрации](custom-policy-rest-api-claims-exchange.md)
- [Защитите свой RESTful API](secure-rest-api.md)
- [Справка: Технический профиль RESTful](restful-technical-profile.md)

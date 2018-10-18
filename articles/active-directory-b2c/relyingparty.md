---
title: Элемент RelyingParty в Azure Active Directory B2C | Документация Майкрософт
description: Сведения об указании элемента RelyingParty настраиваемой политики в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8f56f287fde9e17d2a17298ca04eda63b69e3636
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159930"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элемент **RelyingParty** указывает путь взаимодействия пользователя для обеспечения выполнения текущего запроса в Azure Active Directory (Azure AD) B2C. В нем также указывается список утверждений, которые требуется для приложения проверяющей стороны как часть выданного токена. Приложение проверяющей стороны, например веб-приложение, мобильное или классическое приложение, вызывает файл политики проверяющей стороны. Этот файл выполняет определенную задачу, например вход, сброс пароля или изменение профиля. Несколько приложений могут использовать одну и ту же политику проверяющей стороны, а одно приложение может использовать несколько политик. Все приложения проверяющей стороны получают один и тот же токен с утверждениями, а пользователь проходит один и тот же путь взаимодействия.

В следующем примере показан элемент **RelyingParty** в файле политики *B2C_1A_signup_signin*:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" />
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description> 
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Элемент **RelyingParty** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Путь взаимодействия пользователя по умолчанию для приложения проверяющей стороны. |
| UserJourneyBehaviors | 0:1 | Область действия пути взаимодействия пользователя. |
| TechnicalProfile | 1:1 | Технический профиль, поддерживаемый приложением проверяющей стороны. Технический профиль предоставляет контракт для связи приложения проверяющей стороны с Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Элемент `DefaultUserJourney` указывает ссылку на идентификатор пути взаимодействия пользователя, который обычно определяется в базовой политике или политике расширений. В следующих примерах показан путь взаимодействия пользователя для регистрации или входа в систему, указанный в элементе **RelyingParty**:

Политика *B2C_1A_signup_signin*:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* или *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Элемент **DefaultUserJourney** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Идентификатор пути взаимодействия пользователя в политике. Дополнительные сведения см. в статье [Элемент UserJourneys](userjourneys.md). |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Элемент **UserJourneyBehaviors** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Область действия сеанса единого входа (SSO) в пути взаимодействия пользователя. |
| SessionExpiryType |0:1 | Поведение аутентификации в сеансе. Возможные значения: `Rolling` или `Absolute`. Значение `Rolling` (по умолчанию) указывает, что пользователь остается в системе до тех пор, пока он постоянно работает в приложении. Значение `Absolute` указывает, что пользователь вынужден повторно пройти аутентификацию по истечении периода времени, указанного как время существования сеанса приложения. |
| SessionExpiryInSeconds | 0:1 | Время существования файла cookie сеанса (в виде целого числа) Azure AD B2C, сохраняемого в браузере пользователя при успешной аутентификации. |
| JourneyInsights | 0:1 | Ключ инструментирования Azure Application Insights, который нужно использовать. |
| ContentDefinitionParameters | 0:1 | Список пар "ключ — значение", которые должны быть добавлены к URI загрузки определения содержимого. |

### <a name="singlesignon"></a>SingleSignOn

Элемент **SingleSignOn** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Область | Yes | Область поведение единого входа. Возможные значения: `Suppressed`, `Tenant`, `Application` или `Policy`. Значение `Suppressed` указывает, что поведение подавляется. Например, в случае сеанса единого входа сеанс не поддерживается для пользователя, и пользователь всегда получает запрос на выбор поставщика удостоверений. Значение `TrustFramework` указывает, что поведение применяется для всех политик в инфраструктуре доверия. Например, пользователь, который проходит через пути взаимодействия двух политик для инфраструктуры доверия, не получает запрос на выбор поставщика удостоверений. Значение `Tenant` указывает, что поведение применяется для всех политик в клиенте. Например, пользователь, который проходит через пути взаимодействия двух политик для клиента, не получает запрос на выбор поставщика удостоверений. Значение `Application` указывает, что поведение применяется ко всем политикам для приложения, делающего запрос. Например, пользователь, который проходит через пути взаимодействия двух политик для приложения, не получает запрос на выбор поставщика удостоверений. Значение `Policy` указывает, что поведение применяется только к политике. Например, пользователь, который проходит через пути взаимодействия двух политик для инфраструктуры доверия, получает запрос на выбор поставщика удостоверений при переключении политик. |

## <a name="journeyinsights"></a>JourneyInsights

Элемент **JourneyInsights** содержит следующие атрибуты.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| TelemetryEngine | Yes | Значение должно быть `ApplicationInsights`. | 
| InstrumentationKey | Yes | Строка, содержащая ключ инструментирования для элемента Application Insights. |
| DeveloperMode | Yes | Возможные значения: `true` или `false`. Если параметр имеет значение `true`, Application Insights передает телеметрию через конвейер обработки. Этот параметр подходит для разработки, но ограничен при больших объемах. Подробные журналы действий предназначены только для помощи в разработке настраиваемых политик. Не используйте режим разработки в рабочей среде. В журналах регистрируются все утверждения, отправляемые и принимаемые поставщиками удостоверений во время разработки. При использовании режима разработки в рабочей среде разработчик несет ответственность за персональные данные (личные сведения), собранные в его журнале App Insights. Эти подробные журналы собираются только в том случае, когда этот параметр имеет значение `true`.|
| ClientEnabled | Yes | Возможные значения: `true` или `false`. Если параметр имеет значение `true`, клиентский сценарий Application Insights отправляется для отслеживания представления страницы и ошибок на стороне клиента. | 
| ServerEnabled | Yes | Возможные значения: `true` или `false`. Если параметр имеет значение `true`, отправляется существующие данные JSON UserJourneyRecorder как пользовательское событие в Application Insights. | 
| TelemetryVersion | Yes | Значение должно быть `1.0.0`. | 

Дополнительные сведения см. в статье о [сборе журналов](active-directory-b2c-troubleshoot-custom.md).

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Используя настраиваемые политики в Azure AD B2C, можно отправлять параметр в строке запроса. Передавая параметр в конечную точку HTML, вы можете динамически изменять содержимое страницы. Например, можно изменить фоновое изображение страницы регистрации или входа в Azure AD B2C на основе параметра, передаваемого из веб-приложения или мобильного приложения. Azure AD B2C передает параметры строки запроса в ваш динамический HTML-файл, такой как файл ASPX. 

В следующем примере передается параметр с именем `campaignId` со значением `hawaii` в строке запроса:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Элемент **ContentDefinitionParameters** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Строка, содержащая пару "ключ — значение", которая добавляется к строке запроса в URI загрузки определения содержимого. |

Элемент **ContentDefinitionParameter** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| ИМЯ | Yes | Имя пары "ключ — значение". |

Дополнительные сведения см. в статье [Azure Active Directory B2C: настройка пользовательского интерфейса с динамическим содержимым, используя пользовательские политики](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="technicalprofile"></a>TechnicalProfile

Элемент **TechnicalProfile** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- | 
| Идентификатор | Yes | Значение должно быть `PolicyProfile`. |

Элемент **TechnicalProfile** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Строка с именем технического профиля, отображаемым для пользователей. |
| ОПИСАНИЕ | 0:1 | Строка с описанием технического профиля, отображаемым для пользователей. |
| Протокол | 1:1 | Протокол, используемый для федерации. |
| Метаданные | 0:1 | Коллекция пар "ключ — значение" *элемента*, используемая протоколом для связи с конечной точкой в ​​ходе транзакции для настройки взаимодействия между проверяющей стороной и другими участниками сообщества. |
| OutputClaims | 0:1 | Список типов утверждений, которые используются в качестве выходных данных в техническом профиле. Каждый из этих элементов содержит ссылку на элемент **ClaimType**, уже определенный в разделе **ClaimsSchema** или в политике, из которой этот файл политики наследуется. |
| SubjectNamingInfo | 0:1 | Имя субъекта, используемое в токенах. |

Элемент **Protocol** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| ИМЯ | Yes | Имя допустимого протокола, поддерживаемого в Azure AD B2C и используемого в составе технического профиля. Возможные значения: `OpenIdConnect` или `SAML2`. Значение `OpenIdConnect` представляет собой стандарт протокола OpenID Connect 1.0 согласно спецификации OpenID Foundation. Значение `SAML2` представляет стандартный протокол SAML 2.0 согласно спецификации OASIS. Не используйте токен SAML в рабочей среде. |

## <a name="outputclaims"></a>OutputClaims

Элемент **OutputClaims** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Имя ожидаемого типа утверждения в поддерживаемом списке для политики, на которую подписывается проверяющая сторона. Это утверждение служит в качестве выходных данных технического профиля. |

Элемент **OutputClaim** содержит следующие атрибуты.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Ссылка на **ClaimType**, уже определенная в разделе **ClaimsSchema** файла политики. |
| DefaultValue | Нет  | Значение по умолчанию, которое можно использовать, если значение утверждения пусто. |
| PartnerClaimType | Нет  | Отправляет утверждение с другим именем, указанным в определении ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

С помощью элемента **SubjectNameingInfo** можно управлять значением субъекта токена:
- **Токен JTW** — это утверждение `sub`. Это субъект, в отношении которого токен подтверждает сведения, например пользователь приложения. Это значение является неизменяемым и не может быть переназначено или повторно использовано. Это значение можно использовать для безопасных проверок авторизации, например, когда токен используется для доступа к ресурсу. По умолчанию утверждение субъекта заполняется идентификатором объекта пользователя в каталоге. Дополнительные сведения см. в статье о [настройке токена, сеанса и единого входа](active-directory-b2c-token-session-sso.md).
- **Токен SAML** — это элемент `<Subject><NameID>`, который определяет элемент subject.

Элемент **SubjectNamingInfo** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| ClaimType | Yes | Ссылка на элемент **PartnerClaimType** исходящего утверждения. Исходящие утверждения должны быть определены в коллекции **OutputClaims** политики проверяющей стороны. |

В следующем примере показано, как определить проверяющую сторону OpenID Connect. Сведения об имени субъекта настраиваются как `objectId`:

```XML
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Токен JWT содержит утверждение `sub` с objectId пользователя:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```



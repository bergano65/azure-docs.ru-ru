---
title: Обзор технических профилей в пользовательских политиках
titleSuffix: Azure AD B2C
description: Узнайте, как технические профили используются в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057316"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Технические профили в пользовательских политиках Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Технический профиль предоставляет основу со встроенным механизмом для связи с различными типами сторон, использующих пользовательскую политику в Azure Active Directory B2C (Azure AD B2C). Технические профили служат для обмена данными с клиентом Azure AD B2C, создания пользователя или чтения его профиля. Технический профиль поддерживает самостоятельное подтверждение для взаимодействия с пользователем. Например, он может получать учетные данные пользователя для входа, а затем отображать страницу регистрации или сброса пароля.

## <a name="type-of-technical-profiles"></a>Типы технических профилей

Технический профиль поддерживает следующие типы сценариев:

- [Application Insights](application-insights-technical-profile.md) - Отправка данных о событиях в [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md): обеспечивает управление пользователями Azure Active Directory B2C.
- [Multi-Factor Authentication Azure](multi-factor-auth-technical-profile.md) обеспечивает поддержку проверки номера телефона с помощью Azure Multi-Factor Authentication (MFA). 
- [Преобразование утверждений](claims-transformation-technical-profile.md): вызов преобразований исходящих утверждений для изменения значений утверждений, их проверки или установки значений по умолчанию для набора исходящих утверждений.
- [Издатель маркеров безопасности JWT](jwt-issuer-technical-profile.md): выдает маркер безопасности JWT, который возвращается в приложение проверяющей стороны.
- [OAuth1](oauth1-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 2.0.
- [Одноразовый пароль](one-time-password-technical-profile.md) - обеспечивает поддержку управления генерацией и проверки одноразового пароля.
- [OpenID Connect](openid-connect-technical-profile.md) - Федерация с любым поставщиком идентификационных данных протокола OpenID Connect.
- [Телефонный фактор](phone-factor-technical-profile.md) - Поддержка регистрации и проверки телефонных номеров.
- [ПОСТАВЩИК RESTful](restful-technical-profile.md) - Вызов служб Ы НОС, такие как проверка пользовательского ввода, обогащение пользовательских данных или интеграция с приложениями.
- [SAML2](saml-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу SAML.
- [Эмитент токенов SAML](saml-issuer-technical-profile.md) - излучает токен SAML, который возвращается обратно в приложение для полагающейся стороны.
- [Самостоятельное подтверждение](self-asserted-technical-profile.md): взаимодействие с пользователем. Например, получение учетных данных пользователя для входа, а затем отображение страницы регистрации или сброса пароля.
- [Управление сеансами](custom-policy-reference-sso.md): работа с различными типами сеансов.

## <a name="technical-profile-flow"></a>Процесс работы с техническим профилем

Все типы технических профилей созданы в рамках одной концепции. Вы отправляете входящие утверждения, выполняете их преобразование и обмениваетесь данными с настроенной службой, например поставщиком удостоверений, REST API или службами каталогов Azure AD. После завершения процесса технический профиль возвращает претензии вывода и может запускать преобразование выводных требований. Ниже показана схема обработки трансформаций и сопоставлений, на которые ссылается технический профиль. Независимо от того, с какой службой взаимодействует технический профиль, после преобразования исходящие утверждения технического профиля сразу же сохраняются в хранилище утверждений.

![Диаграмма, иллюстрирующая поток технического профиля](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Управление сеансом Единого включения (SSO)** - Восстанавливает состояние сеанса технического профиля с помощью [управления сеансом SSO.](custom-policy-reference-sso.md)
1. **Преобразование ввода требований** - Заявки на вход каждой [трансформации ввода претензий](claimstransformations.md) подбираются из пакета претензий.  Исходящие утверждения посте преобразования входящих утверждений могут использоваться как входящие утверждения в дальнейших операциях преобразования.
1. **Претензии ввода** - Претензии подбираются из сумки претензий и используются для технического профиля. Например, [технический профиль с самостоятельным подтверждением](self-asserted-technical-profile.md) использует входящие утверждения для предварительного заполнения исходящих утверждений, предоставляемых пользователем. Технический профиль REST API использует входящие утверждения для отправки входящих параметров в конечную точку REST API. Azure Active Directory использует входящее утверждение в качестве уникального идентификатора для чтения, обновления или удаления учетной записи.
1. **Выполнение технического профиля**: технический профиль обменивается утверждениями с настроенной службой. Пример:
    - Пользователь перенаправляется к поставщику удостоверений для завершения процедуры входа. После успешного входа пользователь возвращается назад, и выполнение технического профиля продолжается.
    - Вызов REST API с последующей отправкой параметров в поле InputClaims и получение сведений в поле OutputClaims.
    - Создание или обновление учетной записи пользователя.
    - Отправка и проверка текстовых сообщений многофакторной проверки подлинности.
1. **Технические профили валидации** - [самоутверждаемый технический профиль](self-asserted-technical-profile.md) может вызывать [технические профили проверки.](validation-technical-profile.md) Технический профиль проверки осуществляет проверку данных, предоставленных пользователем, и возвращает сообщение об ошибке либо подтверждение, которое дополнительно может содержать исходящие утверждения. Например, Azure AD B2C перед созданием учетной записи проверяет, существует ли такой пользователь в службах каталогов. Можно вызвать технический профиль REST API для добавления собственной бизнес-логики.<p>Сфера действия претензий технического профиля проверки ограничена техническим профилем, который вызывает технический профиль проверки. и другие технические профили проверки по тому же техническому профилю. Если исходящие утверждения необходимо использовать в следующем шаге оркестрации, необходимо добавить исходящие утверждения в технический профиль, который вызывает технический профиль проверки.
1. **Претензии на выходе** - Претензии возвращаются обратно в пакет претензий. Эти утверждения можно использовать на следующем шаге оркестрации или для преобразования исходящих утверждений.
1. **Преобразования претензий вывода** - Заявки на вход каждого [преобразования вывода претензий](claimstransformations.md) подбираются из пакета претензий. Исходящие утверждения технического профиля, поступающие с предыдущих шагов, могут использоваться в качестве входящих утверждений в операциях преобразования. После выполнения исходящие утверждения возвращаются в хранилище утверждений. Исходящие утверждения после преобразования входящих утверждений могут использоваться в качестве входящих утверждений в следующих операциях преобразования.
1. **Управление сеансами единого включения (SSO)** - Упорствует данные технического профиля к сеансу, используя [управление сеансами SSO.](custom-policy-reference-sso.md)


## <a name="technical-profile-inclusion"></a>Включение технического профиля

Технический профиль может включать в себя другой технический профиль для изменения настроек или добавления новой функциональности.  Элемент `IncludeTechnicalProfile` является отсылкой к базовому техническому профилю, из которого вытекает технический профиль. Ограничение на количество уровней отсутствует.

Например технический профиль **AAD-UserReadUsingAlternativeSecurityId-NoError** включает **AAD-UserReadUsingAlternativeSecurityId**. Этот технический `RaiseErrorIfClaimsPrincipalDoesNotExist` профиль устанавливает элемент `true`метаданных и вызывает ошибку, если социальная учетная запись не существует в каталоге. **AAD-UserReadUsingAlternativeSecurityId-NoError** отменяет это поведение и отменяет это сообщение об ошибке.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** включает технический профиль `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Оба **AAD-UserReadUsingAlternativeSecurityId-NoError** и **AAD-UserUsingAlternativeSecurityId** не указывают необходимый элемент **протокола,** потому что это указано в **AAD-Общие** технический профиль.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

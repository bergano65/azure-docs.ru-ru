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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057316"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Технические профили в пользовательских политиках Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Технический профиль предоставляет платформу со встроенным механизмом взаимодействия с другими типами сторон с помощью настраиваемой политики в Azure Active Directory B2C (Azure AD B2C). Технические профили служат для обмена данными с клиентом Azure AD B2C, создания пользователя или чтения его профиля. Технический профиль поддерживает самостоятельное подтверждение для взаимодействия с пользователем. Например, он может получать учетные данные пользователя для входа, а затем отображать страницу регистрации или сброса пароля.

## <a name="type-of-technical-profiles"></a>Типы технических профилей

Технический профиль поддерживает следующие типы сценариев:

- [Application Insights](application-insights-technical-profile.md) — отправка данных событий в [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md): обеспечивает управление пользователями Azure Active Directory B2C.
- [Многофакторная идентификация Azure](multi-factor-auth-technical-profile.md) . обеспечивает поддержку проверки номера телефона с помощью многофакторной идентификации Azure (MFA). 
- [Преобразование утверждений](claims-transformation-technical-profile.md): вызов преобразований исходящих утверждений для изменения значений утверждений, их проверки или установки значений по умолчанию для набора исходящих утверждений.
- [Издатель маркеров безопасности JWT](jwt-issuer-technical-profile.md): выдает маркер безопасности JWT, который возвращается в приложение проверяющей стороны.
- [OAuth1](oauth1-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 2.0.
- [Одноразовый пароль](one-time-password-technical-profile.md) . обеспечивает поддержку управления поколением и проверкой одноразового пароля.
- [OpenID Connect Connect](openid-connect-technical-profile.md) -Federation с любым поставщиком удостоверений протокола OpenID Connect Connect.
- [Телефонный фактор](phone-factor-technical-profile.md) — поддержка регистрации и проверки номеров телефонов.
- [Поставщик RESTful](restful-technical-profile.md) — вызов служб REST API Services, таких как проверка вводимых пользователем данных, обогащение пользовательской информации или интеграция с бизнес-приложениями.
- [SAML2](saml-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу SAML.
- [Издатель токена SAML](saml-issuer-technical-profile.md) — создает токен SAML, возвращаемый обратно в приложение проверяющей стороны.
- [Самостоятельное подтверждение](self-asserted-technical-profile.md): взаимодействие с пользователем. Например, получение учетных данных пользователя для входа, а затем отображение страницы регистрации или сброса пароля.
- [Управление сеансами](custom-policy-reference-sso.md): работа с различными типами сеансов.

## <a name="technical-profile-flow"></a>Процесс работы с техническим профилем

Все типы технических профилей созданы в рамках одной концепции. Вы отправляете входящие утверждения, выполняете их преобразование и обмениваетесь данными с настроенной службой, например поставщиком удостоверений, REST API или службами каталогов Azure AD. После завершения процесса технический профиль возвращает выходные утверждения и может выполнять преобразование выходных утверждений. Ниже показана схема обработки трансформаций и сопоставлений, на которые ссылается технический профиль. Независимо от того, с какой службой взаимодействует технический профиль, после преобразования исходящие утверждения технического профиля сразу же сохраняются в хранилище утверждений.

![Схема, иллюстрирующая поток технического профиля](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Управление сеансами единого входа (SSO)** — восстанавливает состояние сеанса технического профиля с помощью [управления сеансами единого входа](custom-policy-reference-sso.md).
1. **Преобразование входящих утверждений** . утверждения входных данных всех [преобразований входящих заявок](claimstransformations.md) выбираются из контейнера утверждений.  Исходящие утверждения посте преобразования входящих утверждений могут использоваться как входящие утверждения в дальнейших операциях преобразования.
1. **Входные утверждения** — утверждения выбираются из контейнера утверждений и используются для технического профиля. Например, [технический профиль с самостоятельным подтверждением](self-asserted-technical-profile.md) использует входящие утверждения для предварительного заполнения исходящих утверждений, предоставляемых пользователем. Технический профиль REST API использует входящие утверждения для отправки входящих параметров в конечную точку REST API. Azure Active Directory использует входящее утверждение в качестве уникального идентификатора для чтения, обновления или удаления учетной записи.
1. **Выполнение технического профиля**: технический профиль обменивается утверждениями с настроенной службой. Пример:
    - Пользователь перенаправляется к поставщику удостоверений для завершения процедуры входа. После успешного входа пользователь возвращается назад, и выполнение технического профиля продолжается.
    - Вызов REST API с последующей отправкой параметров в поле InputClaims и получение сведений в поле OutputClaims.
    - Создание или обновление учетной записи пользователя.
    - Отправка и проверка текстовых сообщений многофакторной проверки подлинности.
1. **Технические профили проверки** — [самостоятельно утвержденный технический профиль](self-asserted-technical-profile.md) может вызывать [технические профили проверки](validation-technical-profile.md). Технический профиль проверки осуществляет проверку данных, предоставленных пользователем, и возвращает сообщение об ошибке либо подтверждение, которое дополнительно может содержать исходящие утверждения. Например, Azure AD B2C перед созданием учетной записи проверяет, существует ли такой пользователь в службах каталогов. Можно вызвать технический профиль REST API для добавления собственной бизнес-логики.<p>Область исходящих утверждений технического профиля проверки ограничена техническим профилем, который вызывает технический профиль проверки. и другие технические профили проверки в одном техническом профиле. Если исходящие утверждения необходимо использовать в следующем шаге оркестрации, необходимо добавить исходящие утверждения в технический профиль, который вызывает технический профиль проверки.
1. **Исходящие утверждения** — утверждения возвращаются в контейнер утверждений. Эти утверждения можно использовать на следующем шаге оркестрации или для преобразования исходящих утверждений.
1. Преобразования **исходящих утверждений** . утверждения ввода каждого преобразования исходящих [утверждений](claimstransformations.md) выбираются из контейнера утверждений. Исходящие утверждения технического профиля, поступающие с предыдущих шагов, могут использоваться в качестве входящих утверждений в операциях преобразования. После выполнения исходящие утверждения возвращаются в хранилище утверждений. Исходящие утверждения после преобразования входящих утверждений могут использоваться в качестве входящих утверждений в следующих операциях преобразования.
1. **Управление сеансами единого входа (SSO)** — сохраняет данные технического профиля в сеансе с помощью [управления сеансами единого входа](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Включение технического профиля

Технический профиль может включать еще один технический профиль для изменения параметров или добавления новых функциональных возможностей.  `IncludeTechnicalProfile` Элемент является ссылкой на базовый технический профиль, от которого наследуется технический профиль. Ограничение на количество уровней отсутствует.

Например технический профиль **AAD-UserReadUsingAlternativeSecurityId-NoError** включает **AAD-UserReadUsingAlternativeSecurityId**. Этот технический профиль задает для `RaiseErrorIfClaimsPrincipalDoesNotExist` `true`элемента метаданных значение и вызывает ошибку, если учетная запись социальных сетей не существует в каталоге. **AAD-усерреадусингалтернативесекуритид-Error** переопределяет это поведение и отключает это сообщение об ошибке.

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

Как **AAD-усерреадусингалтернативесекуритид-Error** , так и **AAD-усерреадусингалтернативесекуритид** не указывают требуемый элемент **протокола** , так как он указан в техническом профиле **AAD-Common** .

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

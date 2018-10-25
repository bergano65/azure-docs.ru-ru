---
title: Технические профили в пользовательских политиках Azure Active Directory B2C | Документация Майкрософт
description: Сведения об использовании технических профилей в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe7d18cdfa88988e1c7dda7f1120d4750fa52e8c
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269434"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Технические профили в пользовательских политиках Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Технический профиль предоставляет платформу со встроенным механизмом обмена данными с различными типами служб с помощью пользовательской политики Azure Active Directory (Azure AD) B2C. Технические профили служат для обмена данными с клиентом Azure AD B2C, создания пользователя или чтения его профиля. Технический профиль поддерживает самостоятельное подтверждение для взаимодействия с пользователем. Например, он может получать учетные данные пользователя для входа, а затем отображать страницу регистрации или сброса пароля. 

## <a name="type-of-technical-profiles"></a>Типы технических профилей

Технический профиль поддерживает следующие типы сценариев:

- [Azure Active Directory](active-directory-technical-profile.md): обеспечивает управление пользователями Azure Active Directory B2C.
- [Издатель маркеров безопасности JWT](jwt-issuer-technical-profile.md): выдает маркер безопасности JWT, который возвращается в приложение проверяющей стороны. 
- **Поставщик проверки по телефону**: многофакторная проверка подлинности.
- [OAuth1](oauth1-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу OpenId Connect.
- [Преобразование утверждений](claims-transformation-technical-profile.md): вызов преобразований исходящих утверждений для изменения значений утверждений, их проверки или установки значений по умолчанию для набора исходящих утверждений.
- [Поставщик REST](restful-technical-profile.md): вызов служб REST API, например для проверки данных, вводимых пользователем, дополнения пользовательских данных или интеграции с бизнес-приложениями.
- [SAML2](saml-technical-profile.md): федерация с любым поставщиком удостоверений по протоколу SAML.
- [Самостоятельное подтверждение](self-asserted-technical-profile.md): взаимодействие с пользователем. Например, получение учетных данных пользователя для входа, а затем отображение страницы регистрации или сброса пароля.
- **WsFed**: федерация с любым поставщиком удостоверений по протоколу WsFed. 
- **Управление сеансами**: работа с различными типами сеансов. 
- **Поставщик контекста для пути взаимодействия пользователя**.
- **Application Insights**.

## <a name="technical-profile-flow"></a>Последовательность работы с техническим профилем

Все типы технических профилей созданы в рамках одной концепции. Вы отправляете входящие утверждения, выполняете их преобразование и обмениваетесь данными с настроенной службой, например поставщиком удостоверений, REST API или службами каталогов Azure AD. После завершения процесса технический профиль возвращает исходящие утверждения и может выполнять их преобразование. Ниже показана схема обработки трансформаций и сопоставлений, на которые ссылается технический профиль. Независимо от того, с какой службой взаимодействует технический профиль, после преобразования исходящие утверждения технического профиля сразу же сохраняются в хранилище утверждений. 

![Последовательность работы с техническим профилем](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation**: после каждого [преобразования](claimstransformations.md) входящие утверждения извлекаются из хранилища утверждений и после выполнения исходящие утверждения возвращаются в хранилище. Исходящие утверждения посте преобразования входящих утверждений могут использоваться как входящие утверждения в дальнейших операциях преобразования.
2. **InputClaims**: утверждения извлекаются из хранилища утверждений и используются в техническом профиле. Например, [технический профиль с самостоятельным подтверждением](self-asserted-technical-profile.md) использует входящие утверждения для предварительного заполнения исходящих утверждений, предоставляемых пользователем. Технический профиль REST API использует входящие утверждения для отправки входящих параметров в конечную точку REST API. Azure Active Directory использует входящее утверждение в качестве уникального идентификатора для чтения, обновления или удаления учетной записи.
3. **Выполнение технического профиля**: технический профиль обменивается утверждениями с настроенной службой. Например: 
    - Пользователь перенаправляется к поставщику удостоверений для завершения процедуры входа. После успешного входа пользователь возвращается назад, и выполнение технического профиля продолжается.
    - Вызов REST API с последующей отправкой параметров в поле InputClaims и получение сведений в поле OutputClaims.
    - Создание или обновление учетной записи пользователя.
    - Отправка и проверка текстовых сообщений многофакторной проверки подлинности.
4. **ValidationTechnicalProfiles**: для [технического профиля с самостоятельным подтверждением](self-asserted-technical-profile.md) можно вызывать входящий [технический профиль проверки](validation-technical-profile.md). Технический профиль проверки осуществляет проверку данных, предоставленных пользователем, и возвращает сообщение об ошибке либо подтверждение, которое дополнительно может содержать исходящие утверждения. Например, Azure AD B2C перед созданием учетной записи проверяет, существует ли такой пользователь в службах каталогов. Можно вызвать технический профиль REST API для добавления собственной бизнес-логики.<p>Область действия для исходящих утверждений в техническом профиле проверки ограничена исходным техническим профилем, который вызвал технический профиль проверки, и другими профилями проверки, принадлежащими тому же техническому профилю. Если исходящие утверждения необходимо использовать в следующем шаге оркестрации, необходимо добавить исходящие утверждения в технический профиль, который вызывает технический профиль проверки.
5. **OutputClaims**: утверждения возвращаются в хранилище утверждений. Эти утверждения можно использовать на следующем шаге оркестрации или для преобразования исходящих утверждений.
6. **OutputClaimsTransformations**: входящие утверждения после каждого [преобразования](claimstransformations.md) исходящих утверждений извлекаются из хранилища утверждений. Исходящие утверждения технического профиля, поступающие с предыдущих шагов, могут использоваться в качестве входящих утверждений в операциях преобразования. После выполнения исходящие утверждения возвращаются в хранилище утверждений. Исходящие утверждения после преобразования входящих утверждений могут использоваться в качестве входящих утверждений в следующих операциях преобразования.
7. **Управление сеансами единого входа** - [: служба управления сеансами единого входа](active-directory-b2c-reference-sso-custom.md) контролирует взаимодействие с пользователем после проверки подлинности. Например, администратор может управлять отображением выбранных поставщиков удостоверений или необходимостью повторного ввода данных локальной учетной записи.

Технический профиль может наследовать от другого профиля для изменения параметров или добавления новых функций.  Элемент **IncludeTechnicalProfile** — это ссылка на базовых технический профиль, от которого наследует текущий профиль.  

Например технический профиль **AAD-UserReadUsingAlternativeSecurityId-NoError** включает **AAD-UserReadUsingAlternativeSecurityId**. Технический профиль устанавливает значение элемента метаданных **RaiseErrorIfClaimsPrincipalDoesNotExist** равным `true` и выдает ошибку, если социальная учетная запись отсутствует в каталоге. **AAD-UserReadUsingAlternativeSecurityId-NoError** переопределяет это поведение и отключает сообщение об ошибке, если пользователь не существует.

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

**AAD-UserReadUsingAlternativeSecurityId-NoError** и **AAD-UserReadUsingAlternativeSecurityId** не указывают требуемый элемент **Protocol**, так как он указан в техническом профиле **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Технический профиль может включать другой технический профиль или наследовать от него, а тот, в свою очередь, может включать еще один профиль. Ограничение на количество уровней отсутствует. В зависимости от технических требований путь взаимодействия пользователя может вызывать **AAD-UserReadUsingAlternativeSecurityId**, который выдает сообщение об ошибке, если социальная учетная запись пользователя не существует, или **AAD-UserReadUsingAlternativeSecurityId-NoError**, который не выдает сообщение об ошибке.












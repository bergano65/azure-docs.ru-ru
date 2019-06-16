---
title: Определение самоподтвержденного технического профиля в настраиваемой политике в Azure Active Directory B2C | Документация Майкрософт
description: Определение самоподтвержденного технического профиля в настраиваемой политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512663"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение самоподтвержденного технического профиля в настраиваемой политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Все взаимодействия в Azure Active Directory (Azure AD) B2C, где пользователь должен предоставить входные данные, выполняются с использованием самоподтвержденных технических профилей. Например, страница регистрации, страница входа, страница сброса пароля.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C, для самоподтверждения: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

В следующем примере показан самоподтвержденный технический профиль для регистрации по электронной почте:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Входящие утверждения

В самоподтвержденном техническом профиле можно использовать элементы **InputClaims** и **InputClaimsTransformations**, чтобы предварительно заполнять значение утверждений, которые появляются на странице самоподтверждения (исходящие утверждения). Например, в политике изменения профиля путь взаимодействия пользователя сначала считывает профиль пользователя из службы каталогов Azure AD B2C, а затем самоподтвержденный технический профиль задает входящие утверждения с помощью пользовательских данных, хранящихся в профиле пользователя. Эти утверждения собираются из профиля пользователя, а затем представляются пользователю, который после этого может изменить существующие данные.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список утверждений, которые будут отображаться для сбора данных от пользователя. Чтобы предварительно заполнить исходящие утверждения какими-либо значениями, используйте описанные ранее входящие утверждения. Элемент также может содержать значение по умолчанию. Порядок утверждений в **OutputClaims** управляет порядком отображения утверждений на экране в Azure AD B2C. Атрибут **DefaultValue** вступает в силу только в том случае, если утверждение до этого никогда не задавалось. Однако, если оно было задано на предыдущем шаге оркестрации, даже если пользователь оставляет значение пустым, значение по умолчанию не ступает в силу. Чтобы принудительно использовать значение по умолчанию, задайте атрибуту **AlwaysUseDefaultValue** значение `true`. Чтобы принудить пользователя предоставлять значение для определенного исходящего утверждения, задайте атрибуту **Required** элемента **OutputClaims** значение `true`.

Элемент **ClaimType** в коллекции **OutputClaims** должен задать для элемента **UserInputType** любой тип ввода пользователя, который поддерживается Azure AD B2C, например `TextBox` или `DropdownSingleSelect`. В противном случае элементу **OutputClaim** необходимо задать значение **DefaultValue**.  

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

Следующее исходящее утверждение всегда имеет значение `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Вариант использования

Существует четыре сценария исходящих утверждений:

- **Сбор исходящих утверждений от пользователя**. При необходимости сбора информации от пользователя, такой как дата рождения, необходимо добавить утверждение в коллекцию **OutputClaims**. Для утверждений, которые видит пользователь, необходимо указать **UserInputType**, например `TextBox` или `DropdownSingleSelect`. Если самоподтвержденный технический профиль содержит технический профиль проверки, который выводит то же утверждение, то Azure AD B2C не представляет утверждение пользователю. Если исходящие утверждения, которые можно представить пользователю, отсутствуют, Azure AD B2C пропускает технический профиль.
- **Задание значения по умолчанию в исходящем утверждении**. Без сбора данных от пользователя или возвращения данных из технического профиля проверки. Самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` задает утверждению **executed-SelfAsserted-Input** значение `true`.
- **Технический профиль проверки возвращает исходящие утверждения**. Технический профиль может вызывать технический профиль проверки, который возвращает некоторые утверждения. Вы можете перенаправить утверждения и вернуть их на дальнейшие шаги оркестрации на пути взаимодействия пользователя. Например, при входе в систему с помощью локальной учетной записи самоподтвержденный технический профиль с именем `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки с именем `login-NonInteractive`. Этот технический профиль проверяет учетные данные пользователя и возвращает профиль пользователя. Например, userPrincipalName, displayName, givenName и surName.
- **Вывод утверждений путем преобразования исходящих утверждений**.

В следующем примере самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` демонстрирует использование исходящих утверждений и задает для **executed-SelfAsserted-Input** значение `true`. Утверждения `objectId`, `authenticationSource`, `newUser` являются выходными данными технического профиля проверки `AAD-UserWriteUsingLogonEmail` и не отображаются для пользователя.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Сохранение утверждений

Если элемент **PersistedClaims**отсутствует, самоподтвержденный технический профиль не сохраняет данные в Azure AD B2C. Вместо этого выполняется вызов к техническому профилю проверки, который отвечает за сохранение данных. Например, политика регистрации использует самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` для сбора нового пользовательского профиля. Технический профиль `LocalAccountSignUpWithLogonEmail` вызывает технический профиль проверки для создания учетной записи в Azure AD B2C.

## <a name="validation-technical-profiles"></a>Технические профили проверки

Технический профиль проверки используется для проверки некоторых или всех исходящих утверждений эталонного технического профиля. Входящие утверждения технического профиля проверки должны появиться в исходящих утверждениях самоподтвержденного технического профиля. Технический профиль проверки проверяет входные данные пользователя и возвращает ошибку пользователю. 

Техническим профилем проверки может быть любой технический профиль в политике, например технические профили [Azure Active Directory](active-directory-technical-profile.md) или [REST API](restful-technical-profile.md). В предыдущем примере технический профиль `LocalAccountSignUpWithLogonEmail` проверяет отсутствие имени signinName в каталоге. Если оно отсутствует, технический профиль проверки создает локальную учетную запись и возвращает objectId, authenticationSource и newUser. Технический профиль `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки `login-NonInteractive` для проверки учетных данных пользователя.

Можно также вызывать технический профиль REST API с бизнес-логикой, перезаписывать входящие утверждения или дополнять пользовательские данные за счет дальнейшей интеграции с бизнес-приложением корпоративного уровня. Дополнительные сведения см. в статье [о техническом профиле проверки](validation-technical-profile.md).

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| setting.showContinueButton | Нет | Отображает кнопку "Продолжить". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.showCancelButton | Нет | Отображает кнопку "Отмена". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.operatingMode | Нет | Для страницы входа в систему это свойство управляет поведением поля имени пользователя, таким как проверка входных данных и вывод сообщений об ошибках. Ожидаемые значения: `Username` или `Email`. |
| ContentDefinitionReferenceId | Да | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим техническим профилем. |
| EnforceEmailVerification | Нет | При регистрации или изменении профиля применяет проверку по электронной почте. Возможные значения: `true` (по умолчанию) или `false`. | 
| setting.showSignupLink | Нет | Отображает кнопку "Регистрация". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.retryLimit | Нет | Определяет, сколько раз пользователь может попытаться предоставить данные, что проверяется на соответствие техническому профилю проверки. Например, пользователь пытается зарегистрироваться с помощью учетной записи, которая уже существует, и продолжает попытки до достижения предела.
| SignUpTarget | Нет | Идентификатор обмена целевого объекта регистрации. Когда пользователь нажимает кнопку "Регистрация", Azure AD B2C выполняет указанный идентификатор обмена. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** не используется.














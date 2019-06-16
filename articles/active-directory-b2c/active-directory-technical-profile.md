---
title: Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C | Документация Майкрософт
description: Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b8bbe540d9e296b0f6a0c11a62d3b861e0115d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507445"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C обеспечивает поддержку управления пользователями Azure Active Directory. В этой статье описаны особенности технического профиля для взаимодействия с поставщиком утверждений, который поддерживает этот стандартизированный протокол.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Все технические профили Azure AD включают в себя технический профиль **AAD-Common**. В следующих технических профилях протокол не указывается, так как он настроен в техническом профиле **AAD-Common**:

- **AAD-UserReadUsingAlternativeSecurityId** и **AAD-UserReadUsingAlternativeSecurityId-NoError** — поиск учетной записи социальной сети в каталоге;
- **AAD-UserWriteUsingAlternativeSecurityId** — создание новой учетной записи социальной сети;
- **AAD-UserReadUsingEmailAddress** — поиск локальной учетной записи в каталоге; 
- **AAD-UserWriteUsingLogonEmail** — создание новой локальной учетной записи;
- **AAD-UserWritePasswordUsingObjectId** — обновление пароля локальной учетной записи;
- **AAD-UserWriteProfileUsingObjectId** — обновление профиля пользователя локальной учетной записи или учетной записи социальной сети;
- **AAD-UserReadUsingObjectId** — чтение профиля пользователя локальной учетной записи или учетной записи социальной сети;
- **AAD-UserWritePhoneNumberUsingObjectId** — запись номера телефона MFA локальной учетной записи или учетной записи социальной сети.

В следующем примере показан технический профиль **AAD-Common**:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Входящие утверждения

Следующие технические профили включают в себя входящие утверждения (**InputClaims**) для локальных учетных записей и учетных записей социальных сетей:

- Технические профили учетных записей социальных сетей **AAD-UserReadUsingAlternativeSecurityId** и **AAD-UserWriteUsingAlternativeSecurityId** включают в себя утверждение **AlternativeSecurityId**. Это утверждение содержит идентификатор пользователя учетной записи социальной сети.
- Технические профили локальных учетных записей **AAD-UserReadUsingEmailAddress** и **AAD-UserWriteUsingLogonEmail** включают в себя утверждение **email**. Это утверждение содержит учетное имя локальной учетной записи.
- Унифицированные технические профили (локальных учетных записей и учетных записей социальных сетей) **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId** и **AAD-UserWritePhoneNumberUsingObjectId** включают в себя утверждение **objectId**. Уникальный идентификатор учетной записи.

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation**, которые используются для изменения входных утверждений или создания новых.

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список утверждений, возвращаемых техническим профилем Azure AD. Возможно, вам потребуется сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в Azure Active Directory. Также можно включить утверждения, которые не возвращаются Azure Active Directory, если задан атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения выходных утверждений или создания новых.

Например, технический профиль **AAD-UserWriteUsingLogonEmail** создает локальную учетную запись и возвращает следующие утверждения:

- **objectId** — идентификатор новой учетной записи;
- **newUser** — указывает, является ли пользователь новым;
- **authenticationSource** — задает для проверки подлинности значение `localAccountAuthentication`;
- **userPrincipalName** — имя участника-пользователя новой учетной записи;
- **signInNames.emailAddress** — учетное имя учетной записи, подобное входному утверждению **email**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Элемент **PersistedClaims** содержит все значения, которые должны сохраняться в Azure AD с возможными данными о сопоставлении между типом утверждения, уже определенным в разделе ClaimsSchema политики, и именем атрибута Azure AD. 

Технический профиль **AAD-UserWriteUsingLogonEmail**, который создает новую локальную учетную запись, сохраняет следующие утверждения:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Имя утверждения является именем атрибута Azure AD, если не указан атрибут **PartnerClaimType**, который содержит имя атрибута Azure AD.

## <a name="requirements-of-an-operation"></a>Требования к операции

- В контейнере утверждений для всех технических профилей Azure AD должен содержаться ровно один элемент **InputClaim**. 
- Для операций `Write` или `DeleteClaims` он также должен быть указан в элементе **PersistedClaims**.
- Значение утверждения **userPrincipalName** должно быть в формате `user@tenant.onmicrosoft.com`.
- Утверждение **DisplayName** является обязательным и не может быть пустой строкой.

## <a name="azure-ad-technical-provider-operations"></a>Операции технического поставщика Azure AD

### <a name="read"></a>Чтение

Операция **Read** считывает данные об одной учетной записи пользователя. Для чтения данных пользователя в качестве входного утверждения необходимо указать ключ, например **objectId**, **userPrincipalName**, **signInNames** (любой тип, имя пользователя и учетная запись на основе электронной почты) или **alternativeSecurityId**.  

Следующий технический профиль считывает данные об учетной записи пользователя с использованием objectId пользователя:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>запись

Операция **Write** создает или обновляет одну учетную запись пользователя. Чтобы записать учетную запись пользователя, в качестве входного утверждения необходимо указать ключ, например **objectId**, **userPrincipalName**, **signInNames.emailAddress** или **alternativeSecurityId**.  

Следующий технический профиль создает новую учетную запись социальной сети:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Операция **DeleteClaims** удаляет данные из указанного списка утверждений. Чтобы удалить данные из утверждений, в качестве входного утверждения необходимо указать ключ, например **objectId**, **userPrincipalName**, **signInNames.emailAddress** или **alternativeSecurityId**.  

Следующий технический профиль удаляет утверждения:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Операция **DeleteClaimsPrincipal** удаляет одну учетную запись пользователя из каталога. Чтобы удалить учетную запись пользователя, в качестве входного утверждения необходимо указать ключ, например **objectId**, **userPrincipalName**, **signInNames.emailAddress** или **alternativeSecurityId**.  

Следующий технический профиль удаляет учетную запись пользователя из каталога, используя имя участника-пользователя:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Следующий технический профиль удаляет учетную запись пользователя социальной сети с помощью **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Операция, которая выполняется. Возможные значения: `Read`, `Write`, `DeleteClaims` или `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Нет | Вызывает ошибку, если объект пользователя не существует в каталоге. Возможные значения: `true` или `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Нет | Если вызывается ошибка (см. описание атрибута RaiseErrorIfClaimsPrincipalDoesNotExist), укажите сообщение, которое будет показано пользователю, если объект пользователя не существует. Значение может быть [локализовано](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Нет | Вызывает ошибку, если объект пользователя уже существует. Возможные значения: `true` или `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Нет | Если вызывается ошибка (см. описание атрибута RaiseErrorIfClaimsPrincipalAlreadyExists), укажите сообщение, которое будет показано пользователю, если объект пользователя уже существует. Значение может быть [локализовано](localization.md).| 
| ApplicationObjectId | Нет | Идентификатор объекта приложения для атрибутов расширения. Значение: ObjectId приложения. Дополнительные сведения см. в статье [Использование настраиваемых атрибутов в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Нет | Идентификатор клиента для доступа к арендатору в качестве третьей стороны. Дополнительные сведения см. в статье [Использование настраиваемых атрибутов в пользовательской политике изменения профиля](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 















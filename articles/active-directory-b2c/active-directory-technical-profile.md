---
title: Определение технического профиля Azure AD в пользовательской политике
titleSuffix: Azure AD B2C
description: Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330384"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку управления пользователями Active Directory Azure. В этой статье описаны особенности технического профиля для взаимодействия с поставщиком утверждений, который поддерживает этот стандартизированный протокол.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

В соответствии с [пользовательским пакетом зачета Azure](custom-policy-get-started.md#custom-policy-starter-pack) AD технические профили включают технический профиль **AAD-Common.** Технические профили Azure AD не указывают протокол, поскольку протокол настроен в техническом профиле **AAD-Common:**
 
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

## <a name="inputclaims"></a>InputClaims

Элемент InputClaims содержит претензию, которая используется для поиска учетной записи в каталоге или создания новой. Для всех технических профилей Azure AD в коллекции входных требований должен быть точно один элемент Ввода. Возможно, вам потребуется сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в Azure Active Directory.

Для чтения, обновления или удаления существующей учетной записи пользователя претензия ввода является ключом, который однозначно идентифицирует учетную запись в каталоге Azure AD. Например, **objectId,** **userPrincipalName,** **signInNames.emailАдрес,** **signInNames.userName**, или **alternativeSecurityId**. 

Для создания новой учетной записи пользователя претензия на ввод является ключом, который однозначно идентифицирует локальную или федеративную учетную запись. Например, локальная учетная запись: **signInNames.emailАдрес**или **signInNames.userName**. Для федеративного счета: **alternativeSecurityId**.

Элемент [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) может содержать набор элементов преобразования входных требований, которые используются для изменения претензии ввода или создания нового.

## <a name="outputclaims"></a>OutputClaims

Элемент **OutputClaims** содержит список утверждений, возвращаемых техническим профилем Azure AD. Возможно, вам потребуется сопоставить имя утверждения, определенное в вашей политике, с именем, определенным в Azure Active Directory. Также можно включить утверждения, которые не возвращаются Azure Active Directory, если задан атрибут `DefaultValue`.

Элемент [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

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

Элемент **PersistedClaims** содержит все значения, которые должны сохраняться Azure AD с возможной информацией о отображении между типом претензий, уже определенным в разделе [ClaimsSchema](claimsschema.md) в политике, и именем атрибута Azure AD.

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
- [Статья атрибутов профиля пользователя](user-profile-attributes.md) описывает поддерживаемые атрибуты профиля пользователя Azure AD B2C, которые можно использовать в входного запроса, выводах и сохраняющиеся претензии. 
- Для операций `Write` или `DeleteClaims` он также должен быть указан в элементе **PersistedClaims**.
- Значение утверждения **userPrincipalName** должно быть в формате `user@tenant.onmicrosoft.com`.
- Утверждение **DisplayName** является обязательным и не может быть пустой строкой.

## <a name="azure-ad-technical-provider-operations"></a>Операции технического поставщика Azure AD

### <a name="read"></a>Чтение

Операция **Read** считывает данные об одной учетной записи пользователя. Следующий технический профиль считывает данные об учетной записи пользователя с использованием objectId пользователя:

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

Операция **Write** создает или обновляет одну учетную запись пользователя. Следующий технический профиль создает новую учетную запись социальной сети:

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

Операция **DeleteClaims** удаляет данные из указанного списка утверждений. Следующий технический профиль удаляет утверждения:

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

Операция **DeleteClaimsPrincipal** удаляет одну учетную запись пользователя из каталога. Следующий технический профиль удаляет учетную запись пользователя из каталога, используя имя участника-пользователя:

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

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Операция, которая выполняется. Возможные значения: `Read`, `Write`, `DeleteClaims` или `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | нет | Вызывает ошибку, если объект пользователя не существует в каталоге. Возможные значения: `true` или `false`. |
| RaiseErrorIfClaimsPrincipalAlreadyExists | нет | Вызывает ошибку, если объект пользователя уже существует. Возможные значения: `true` или `false`.|
| ApplicationObjectId | нет | Идентификатор объекта приложения для атрибутов расширения. Значение: ObjectId приложения. Для получения дополнительной [информации см. Используйте пользовательские атрибуты в политике изменения пользовательского профиля.](custom-policy-custom-attributes.md) |
| ClientId | нет | Идентификатор клиента для доступа к арендатору в качестве третьей стороны. Дополнительные сведения см. в статье [Использование настраиваемых атрибутов в пользовательской политике изменения профиля](custom-policy-custom-attributes.md) |
| IncludeclaimРазрешениеОбработкапретензий  | нет | Для претензий на ввод и вывод ы указывается, включено ли [разрешение претензий](claim-resolver-overview.md) в технический профиль. Возможные значения: `true` `false`  или (по умолчанию). Если вы хотите использовать решатели претензий в `true`техническом профиле, установите это на . |

### <a name="ui-elements"></a>Элементы пользовательского интерфейса
 
Следующие настройки можно использовать для настройки сообщения об ошибке, отображаемого при сбое. Метаданные должны быть настроены в [самоутверждаемом](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках могут быть [локализованы.](localization.md)

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | нет | Если вызывается ошибка (см. описание атрибута RaiseErrorIfClaimsPrincipalAlreadyExists), укажите сообщение, которое будет показано пользователю, если объект пользователя уже существует. |
| UserMessageIfClaimsPrincipalDoesNotExist | нет | Если вызывается ошибка (см. описание атрибута RaiseErrorIfClaimsPrincipalDoesNotExist), укажите сообщение, которое будет показано пользователю, если объект пользователя не существует. |


## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующую статью, например, об использовании технического профиля Azure AD:

- [Добавление утверждений и настройка пользовательского ввода с помощью настраиваемых политик в Azure Active Directory B2C](custom-policy-configure-user-input.md)















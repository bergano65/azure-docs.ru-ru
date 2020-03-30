---
title: Управление сеансами единого регистра с использованием пользовательских политик
titleSuffix: Azure AD B2C
description: Узнайте, как для управлять сеансами единого входа с помощью настраиваемых политик в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246036"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Управление сеансами единого входа в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Управление сеансами единого вступления в систему (SSO) в Azure Active Directory B2C (Azure AD B2C) позволяет администратору контролировать взаимодействие с пользователем после того, как пользователь уже подтвердил подлинность. Например, администратор может управлять отображением выбранных поставщиков удостоверений или необходимостью повторного ввода данных локальной учетной записи. В этой статье описывается настройка параметров единого входа для Azure AD B2C.

Управление сеансами единого входа состоит из двух частей. Первая относится к непосредственному взаимодействию пользователя с Azure AD B2C, а вторая — к взаимодействию пользователя с внешними сторонами, например Facebook. Azure AD B2C не переопределяет и не обходит сеансы единого входа, которые могут поддерживаться внешними сторонами. Скорее маршрут через Azure AD B2C, чтобы добраться до внешней стороны, «запоминается», избегая необходимости повторного провоцирования пользователя, чтобы выбрать поставщика социальных или корпоративных идентификационных данных. Окончательный выбор сеанса единого входа остается за внешней стороной.

Для управления сеансами единого входа используется та же семантика, что и для любого другого технического профиля в настраиваемых политиках. При выполнении шага оркестрации из технического профиля, связанного с шагом, запрашиваются ссылка на `UseTechnicalProfileForSessionManagement`. Затем, при наличии такового, указанный поставщик сеансов единого входа проверяется, чтобы определить, является ли пользователь участником сеанса. Если это так, то поставщик сеансов единого входа используется для повторного заполнения данных сеанса. Аналогичным образом после выполнения шага оркестрации поставщик используется для сохранения сведений в сеансе, если был указан поставщик сеансов единого входа.

В Azure AD B2C можно использовать определенное количество поставщиков сеансов единого входа.

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Классы управления единым входом указываются с помощью элемента `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` технического профиля.

## <a name="input-claims"></a>Входящие утверждения

Элемент `InputClaims` пуст или отсутствует.

## <a name="persisted-claims"></a>Неупорные претензии

Претензии, которые должны быть возвращены в приложение или использованы предварительными условиями в последующих шагах, должны храниться в сеансе или дополняться чтением из профиля пользователя в каталоге. Использование упорных утверждений гарантирует, что ваши поездки по аутентификации не помогут при недостающих требованиях. Чтобы добавить утверждения в сеанс, используйте элемент `<PersistedClaims>` технического профиля. При использовании этого поставщика для повторного заполнения данных сеанса сохраненные утверждения добавляются в контейнер утверждений.

## <a name="output-claims"></a>Исходящие утверждения

Используется `<OutputClaims>` для извлечения утверждений из сеанса.

## <a name="session-providers"></a>Поставщики сеансов

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Как можно понять из названия, этот поставщик не выполняет никаких действий. Его можно использовать для подавления реакции службы единого входа на события для конкретного технического профиля. Следующий `SM-Noop` технический профиль включен в [пакет пользовательских политик.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Этот поставщик можно использовать для хранения утверждений в сеансе. Как правило, этот поставщик указывается в техническом профиле, используемом для управления локальными учетными записями. Следующий `SM-AAD` технический профиль включен в [пакет пользовательских политик.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Следующий `SM-MFA` технический профиль включен в [пакет пользовательских](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`политик. Этот технический профиль управляет многофакторной сессией аутентификации.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Этот провайдер используется для подавления экрана "выбрать поставщика идентификации". Обычно он указывается в техническом профиле, настроенном для внешнего поставщика удостоверений, например Facebook. Следующий `SM-SocialLogin` технический профиль включен в [пакет пользовательских политик.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание|
| --- | --- | --- |
| ВсегдаFetchclaimsFromProvider | нет | В настоящее время не используется, может быть проигнорировано. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Этот провайдер используется для управления сессиями Azure AD B2C SAML между приложением для полагающейся стороны или федеративным поставщиком удостоверений SAML. При использовании поставщика SSO для хранения сеанса `RegisterServiceProviders` поставщика идентификационных `false`данных SAML необходимо настроить. Следующий `SM-Saml-idp` технический профиль используется [техническим профилем SAML.](saml-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

При использовании поставщика для хранения сессии B2C `RegisterServiceProviders` SAML `true`необходимо установить. Для завершения выхода из сеанса SAML требуются `SessionIndex` и `NameID`.

Следующий `SM-Saml-idp` технический профиль используется [техническим профилем эмитента SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание|
| --- | --- | --- |
| IncludeSessionIndex | нет | В настоящее время не используется, может быть проигнорировано.|
| RegisterServiceProviders | нет | Указывает, что поставщик должен зарегистрировать все поставщики услуг SAML, которыми было выдано утверждение. Возможные значения: `true` (по умолчанию) или `false`.|




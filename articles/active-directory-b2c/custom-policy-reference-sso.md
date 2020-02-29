---
title: Управление сеансами единого входа с помощью пользовательских политик
titleSuffix: Azure AD B2C
description: Узнайте, как для управлять сеансами единого входа с помощью настраиваемых политик в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a64af5d2b19b05ec9a5eda97c43e278cdfb8b4ff
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189112"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Управление сеансами единого входа в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Управление сеансами единого входа (SSO) в Azure Active Directory B2C (Azure AD B2C) позволяет администратору управлять взаимодействием с пользователем после того, как пользователь уже прошел проверку подлинности. Например, администратор может управлять отображением выбранных поставщиков удостоверений или необходимостью повторного ввода данных локальной учетной записи. В этой статье описывается настройка параметров единого входа для Azure AD B2C.

Управление сеансами единого входа состоит из двух частей. Первая относится к непосредственному взаимодействию пользователя с Azure AD B2C, а вторая — к взаимодействию пользователя с внешними сторонами, например Facebook. Azure AD B2C не переопределяет и не обходит сеансы единого входа, которые могут поддерживаться внешними сторонами. Вместо этого маршрут для доступа к внешней стороне, проходящий через Azure AD B2C, "запоминается", устраняя необходимость повторно предлагать пользователю выбрать поставщик удостоверений социальных сетей или поставщик корпоративных удостоверений. Окончательный выбор сеанса единого входа остается за внешней стороной.

Для управления сеансами единого входа используется та же семантика, что и для любого другого технического профиля в настраиваемых политиках. При выполнении шага оркестрации из технического профиля, связанного с шагом, запрашиваются ссылка на `UseTechnicalProfileForSessionManagement`. Затем, при наличии такового, указанный поставщик сеансов единого входа проверяется, чтобы определить, является ли пользователь участником сеанса. Если это так, то поставщик сеансов единого входа используется для повторного заполнения данных сеанса. Аналогичным образом после выполнения шага оркестрации поставщик используется для сохранения сведений в сеансе, если был указан поставщик сеансов единого входа.

В Azure AD B2C можно использовать определенное количество поставщиков сеансов единого входа.

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Классы управления единым входом указываются с помощью элемента `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` технического профиля.

## <a name="input-claims"></a>Входящие утверждения

Элемент `InputClaims` пуст или отсутствует.

## <a name="persisted-claims"></a>Материализованные утверждения

Утверждения, которые должны быть возвращены в приложение или использоваться предусловиями в последующих шагах, должны храниться в сеансе или дополняться считыванием из профиля пользователя в каталоге. Использование материализованных утверждений гарантирует, что пути проверки подлинности не будут завершаться сбоем в случае отсутствия утверждений. Чтобы добавить утверждения в сеанс, используйте элемент `<PersistedClaims>` технического профиля. При использовании этого поставщика для повторного заполнения данных сеанса сохраненные утверждения добавляются в контейнер утверждений.

## <a name="output-claims"></a>Исходящие утверждения

`<OutputClaims>` используется для получения утверждений из сеанса.

## <a name="session-providers"></a>Поставщики сеансов

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Как можно понять из названия, этот поставщик не выполняет никаких действий. Его можно использовать для подавления реакции службы единого входа на события для конкретного технического профиля. Следующий `SM-Noop` технический профиль включен в [начальный пакет пользовательской политики](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Этот поставщик можно использовать для хранения утверждений в сеансе. Как правило, этот поставщик указывается в техническом профиле, используемом для управления локальными учетными записями. Следующий `SM-AAD` технический профиль включен в [начальный пакет пользовательской политики](custom-policy-get-started.md#custom-policy-starter-pack).

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

Следующий `SM-MFA` технический профиль включен в `SocialAndLocalAccountsWithMfa`[начальный пакет пользовательской политики](custom-policy-get-started.md#custom-policy-starter-pack) . Этот технический профиль управляет сеансом многофакторной проверки подлинности.

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

Этот поставщик используется для подавления экрана "Выбор поставщика удостоверений". Обычно он указывается в техническом профиле, настроенном для внешнего поставщика удостоверений, например Facebook. Следующий `SM-SocialLogin` технический профиль включен в [начальный пакет пользовательской политики](custom-policy-get-started.md#custom-policy-starter-pack).

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

| attribute | Обязательно | Description|
| --- | --- | --- |
| алвайсфетчклаимсфромпровидер | нет | В настоящее время не используется, может игнорироваться. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Этот поставщик используется для управления Azure AD B2C сеансов SAML между приложением проверяющей стороны или федеративным поставщиком удостоверений SAML. При использовании поставщика единого входа для хранения сеанса поставщика удостоверений SAML для `IncludeSessionIndex` и `RegisterServiceProviders` необходимо задать значение `false`. В [техническом профиле SAML](saml-technical-profile.md)используется следующий `SM-Saml-idp` технический профиль.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

При использовании поставщика для хранения сеанса SAML B2C `IncludeSessionIndex` и `RegisterServiceProviders` должны иметь значение `true`. Для завершения выхода из сеанса SAML требуются `SessionIndex` и `NameID`.

[Технический профиль SAML Issuer](connect-with-saml-service-providers.md) использует следующий `SM-Saml-idp` технический профиль

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Метаданные

| attribute | Обязательно | Description|
| --- | --- | --- |
| IncludeSessionIndex | нет | Указывает поставщику, что следует сохранить индекс сеанса. Возможные значения: `true` (по умолчанию) или `false`.|
| RegisterServiceProviders | нет | Указывает, что поставщик должен зарегистрировать все поставщики услуг SAML, которыми было выдано утверждение. Возможные значения: `true` (по умолчанию) или `false`.|




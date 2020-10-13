---
title: Настройка поведения сеанса с помощью настраиваемых политик — Azure Active Directory B2C | Документация Майкрософт
description: Настройка поведения сеанса с помощью пользовательских политик в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961108"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Настройка поведения сеанса с помощью пользовательских политик в Azure Active Directory B2C

Управление [сеансами единого входа (SSO)](session-overview.md) в Azure Active Directory B2C (Azure AD B2C) позволяет администратору управлять взаимодействием с пользователем после того, как пользователь уже прошел проверку подлинности. Например, администратор может управлять отображением выбранных поставщиков удостоверений, а также указывать, нужно ли повторно указывать сведения об учетной записи. В этой статье описывается настройка параметров единого входа для Azure AD B2C.

## <a name="session-behavior-properties"></a>Свойства поведения сеанса

Следующие свойства можно использовать для управления сеансами веб-приложения.

- **Время существования сеанса веб-приложения (в минутах)**. Время существования файла cookie сеанса Azure AD B2C, сохраненного в браузере пользователя после успешной проверки подлинности.
  - Значение по умолчанию — 86400 секунд (1440 минут).
  - Минимум (включительно) = 900 секунд (15 минут).
  - Максимум (включительно) = 86400 с (1440 минут).
- **Время ожидания сеанса веб-приложения** — [тип срока действия сеанса](session-overview.md#session-expiry-type), *пошаговый*или *абсолютный*. 
- **Конфигурация единого входа** . [область сеанса](session-overview.md#session-scope) поведения единого входа (SSO) для нескольких приложений и пользовательских потоков в клиенте Azure AD B2C. 

## <a name="configure-the-properties"></a>Настройка свойств

Чтобы изменить конфигурации режима работы сеанса и SSO, добавьте элемент **UserJourneyBehaviors** в элемент [RelyingParty](relyingparty.md).  Элемент **UserJourneyBehaviors** должен сразу же следовать за элементом **DefaultUserJourney**. Элемент **усержаурнэйбехаворс** должен выглядеть, как в следующем примере:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>Настройка поведения выхода

### <a name="secure-your-logout-redirect"></a>Защита перенаправления выхода

После выхода пользователя перенаправляется на URI, указанный в `post_logout_redirect_uri` параметре, независимо от URL-адресов ответа, указанных для приложения. Однако если `id_token_hint` передано допустимое значение, а **маркер обязательного идентификатора в запросах выхода** включен, Azure AD B2C проверяет, соответствует ли значения `post_logout_redirect_uri` одному из настроенных URI перенаправления приложения перед выполнением перенаправления. Если соответствующий URL-адрес ответа не был настроен для приложения, выводится сообщение об ошибке, и пользователь не перенаправляется. 

Чтобы запросить маркер идентификации в запросах выхода, добавьте элемент **усержаурнэйбехавиорс** в элемент [релингпарти](relyingparty.md) . Затем задайте для **енфорцеидтокенхинтонлогаут** элемента **SingleSignOn** значение `true` . Элемент **усержаурнэйбехавиорс** должен выглядеть, как в следующем примере:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

Чтобы настроить URL-адрес выхода из приложения, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. Выберите **Регистрация приложений**, а затем выберите свое приложение.
1. Выберите **Проверка подлинности**.
1. В текстовом поле **logout URL** (текст для выхода) введите URI перенаправления выхода и нажмите кнопку **сохранить**.

### <a name="single-sign-out"></a>Единый выход

#### <a name="configure-the-applications"></a>Настройка приложений

При перенаправлении пользователя в конечную точку выхода Azure AD B2C (для протоколов OAuth2 и SAML) Azure AD B2C очищает сеанс пользователя из браузера.  Чтобы разрешить [единый выход](session-overview.md#single-sign-out), задайте для `LogoutUrl` приложения портал Azure:

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Выберите каталог Azure AD B2C, щелкнув свою учетную запись в правом верхнем углу страницы.
1. В меню слева выберите **Azure AD B2C**, выберите **Регистрация приложений**, а затем выберите свое приложение.
1. Выберите **Проверка подлинности**.
1. В текстовом поле **logout URL** (текст для выхода) введите URI перенаправления выхода и нажмите кнопку **сохранить**.

#### <a name="configure-the-token-issuer"></a>Настройка издателя маркера 

Для поддержки единого выхода в технический профиль издателя маркера для JWT и SAML необходимо указать:

- Имя протокола, например `<Protocol Name="OpenIdConnect" />`
- Ссылка на технический профиль сеанса, например `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

В следующем примере показаны издатели маркеров JWT и SAML с единым выходом:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [Azure AD B2C сеансе](session-overview.md).

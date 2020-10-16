---
title: Настройка прямого входа в систему с помощью Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настроить предварительное заполнение имени для входа или перенаправление непосредственно в поставщик удостоверений в социальных сетях.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388601"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Настройка прямого входа в систему с помощью Azure Active Directory B2C

При настройке входа для приложения с помощью Azure Active Directory (AD) B2C вы можете настроить предварительное заполнение имени для входа или прямой вход в поставщик удостоверений в определенных социальных сетях, например Facebook или LinkedIn, или учетной записи Майкрософт.

## <a name="prepopulate-the-sign-in-name"></a>Предварительное заполнение имени входа в систему

Во время входа пользователя в систему приложение проверяющей стороны может предложить определенное имя пользователя или домена. При этом приложение может указать параметр запроса `login_hint` с именем пользователя в запросе на авторизацию. Azure AD B2C автоматически заполняет имя для входа в систему — пользователю нужно ввести только пароль.

![Страница входа с login_hint параметром запроса, выделенным в URL-адресе](./media/direct-signin/login-hint.png)

Пользователь может изменить значение, отображаемое в текстовом поле, для входа в систему.

Если вы используете пользовательскую политику, переопределите технический профиль `SelfAsserted-LocalAccountSignin-Email`. В разделе `<InputClaims>` задайте для DefaultValue утверждения signInName значение `{OIDC:LoginHint}`. Переменная `{OIDC:LoginHint}` содержит значение параметра `login_hint`. Azure AD B2C считывает значение утверждения signInName и заполняет текстовое поле signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Перенаправление операции входа в поставщик социальных сетей

Если вы настроили для приложения возможность входа с использованием учетных записей социальных сетей, таких как Facebook, LinkedIn или Google, вы можете указать параметр `domain_hint`. Этот параметр запроса сообщает Azure AD B2C о поставщике удостоверений социальных сетей, который должен использоваться для входа в систему. Например, если приложение указывает `domain_hint=facebook.com`, вход в систему выполняется непосредственно на странице входа в Facebook.

![Страница входа с domain_hint параметром запроса, выделенным в URL-адресе](./media/direct-signin/domain-hint.png)

Если вы используете пользовательскую политику, вы можете настроить доменное имя с помощью XML-элемента `<Domain>domain name</Domain>` любого `<ClaimsProvider>`.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```



---
title: Определение технического профиля OAuth1 в пользовательской политике в Azure Active Directory B2C | Документация Майкрософт
description: Определите технический профиль OAuth1 в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 97fa5757f8b77e29545f6d6f6b885334c7b526f1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063993"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля OAuth1 в настраиваемой политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку поставщика удостоверений [протокола OAuth 1,0](https://tools.ietf.org/html/rfc5849) . В этой статье описаны особенности технического профиля для взаимодействия с поставщиком утверждений, который поддерживает этот стандартизированный протокол. С помощью технического профиля OAuth1 можно создать федерацию с помощью поставщика удостоверений на основе OAuth1, например Twitter. Федерацию с поставщиком удостоверений позволяет пользователям входить в систему с помощью существующих социальных или корпоративных удостоверений.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `OAuth1`. Например, `OAuth1` — это протокол для технического профиля **Twitter-OAUTH1**.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Входящие утверждения

Элементы **InputClaims** и **InputClaimsTransformations** являются пустыми или отсутствуют.

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список утверждений, возвращаемых поставщиком удостоверений OAuth1. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным у поставщика удостоверений. Вы также можете добавить утверждения, которые не возвращаются поставщиком удостоверений, установив атрибут **DefaultValue**.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

В этом примере показаны утверждения, возвращаемые поставщиком удостоверений Twitter:

- Утверждение о недействительности, сопоставленное **с утверждением** **issuerUserId** .
- Утверждение **screen_name**, которое сопоставляется с утверждением **displayName**.
- Утверждение **email** не сопоставляется с именем.

Технический профиль также возвращает утверждения, которые не возвращаются поставщиком удостоверений:

- Утверждение **IdentityProvider**, содержащее имя поставщика удостоверений.
- Утверждение **AuthenticationSource** со значением по умолчанию `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательное значение | Описание |
| --------- | -------- | ----------- |
| client_id | Да | Идентификатор приложения поставщика удостоверений. |
| ProviderName | Нет | Имя поставщика удостоверений. |
| request_token_endpoint | Да | URL-адрес конечной точки маркера запроса согласно RFC 5849. |
| authorization_endpoint | Да | URL-адрес конечной точки авторизации согласно RFC 5849. |
| access_token_endpoint | Да | URL-адрес конечной точки маркера согласно RFC 5849. |
| ClaimsEndpoint | Нет | URL-адрес конечной точки информации о пользователе. |
| ClaimsResponseFormat | Нет | Формат ответа утверждений.|

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** содержит следующий атрибут:

| Атрибут | Обязательное значение | Описание |
| --------- | -------- | ----------- |
| client_secret | Да | Секрет клиента приложения поставщика удостоверений.   |

## <a name="redirect-uri"></a>URI перенаправления

При настройке URL-адреса перенаправления поставщика удостоверений введите `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Замените **tenant** именем своего клиента (например contosob2c.onmicrosoft.com), а **policyId** — идентификатором политики (например b2c_1a_policy). URI перенаправления должен содержать только строчные символы. Добавьте URL-адрес перенаправления для всех политик, использующих имя входа поставщика удостоверений.

Если вы используете домен **b2clogin.com** вместо **login.microsoftonline.com**, удостоверьтесь, что єто действительно требуется.

Примеры:

- [Azure Active Directory B2C. Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик](active-directory-b2c-custom-setup-twitter-idp.md)














---
title: Определить OAuth1 технического профиля в настраиваемую политику в Azure Active Directory B2C | Документация Майкрософт
description: Определите OAuth1 технического профиля в настраиваемую политику в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3fe839de8cbaa0b321b0b0602b000b7575224dde
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010064"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определить OAuth1 технического профиля в настраиваемую политику Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C поддерживает [протокол OAuth 1.0](https://tools.ietf.org/html/rfc5849) для поставщиков удостоверений. В этой статье описаны особенности технического профиля для взаимодействия с поставщиком утверждений, который поддерживает этот стандартизированный протокол. С помощью OAuth1 технического профиля можно использовать для федерации с поставщиком удостоверений на основе OAuth1, таких как Twitter. Федеративные отношения с поставщиком удостоверений позволяет пользователям вход с помощью существующие социальных сетей или корпоративными удостоверениями.

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

- **User_id** утверждения, который сопоставляется с **issuerUserId** утверждения.
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

| Атрибут | Обязательно для заполнения | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| client_id | Yes | Идентификатор приложения поставщика удостоверений. |
| ProviderName | Нет  | Имя поставщика удостоверений. |
| request_token_endpoint | Yes | URL-адрес конечной точки маркера запроса согласно RFC 5849. |
| authorization_endpoint | Yes | URL-адрес конечной точки авторизации согласно RFC 5849. |
| access_token_endpoint | Yes | URL-адрес конечной точки маркера согласно RFC 5849. |
| ClaimsEndpoint | Нет  | URL-адрес конечной точки информации о пользователе. | 
| ClaimsResponseFormat | Нет  | Формат ответа утверждений.|

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| client_secret | Yes | Секрет клиента приложения поставщика удостоверений.   | 

## <a name="redirect-uri"></a>URI перенаправления

При настройке URL-адреса перенаправления поставщика удостоверений введите `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Замените **tenant** именем своего клиента (например contosob2c.onmicrosoft.com), а **policyId** — идентификатором политики (например b2c_1a_policy). URI перенаправления должен содержать только строчные символы. Добавьте URL-адрес перенаправления для всех политик, использующих имя входа поставщика удостоверений. 

Если вы используете домен **b2clogin.com** вместо **login.microsoftonline.com**, удостоверьтесь, что єто действительно требуется.

Примеры:

- [Azure Active Directory B2C. Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик](active-directory-b2c-custom-setup-twitter-idp.md)














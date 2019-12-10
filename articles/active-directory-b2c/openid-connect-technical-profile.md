---
title: Определение технического профиля OpenID Connect Connect в пользовательской политике
titleSuffix: Azure AD B2C
description: Определите технический профиль OpenID Connect Connect в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdb925f820cad79fe68e7082f4ed63292a7d9444
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951094"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля OpenID Connect Connect в Azure Active Directory B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку поставщика удостоверений протокола [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1.0 определяет уровень идентификации поверх OAuth 2.0 и представляет собой оптимизированный протокол среди современных протоколов аутентификации. С помощью технического профиля OpenID Connect Connect можно создать федерацию с помощью поставщика удостоверений на основе OpenID Connect, например Azure AD. Федерацию с поставщиком удостоверений позволяет пользователям входить в систему, используя существующие социальные или корпоративные удостоверения.

## <a name="protocol"></a>Протокол

Для атрибута **Name** элемента **Protocol** необходимо задать значение `OpenIdConnect`. Например, `OpenIdConnect` — это протокол для технического профиля **MSA-OIDC**.

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Входящие утверждения

Элементы **InputClaims** и **InputClaimsTransformations** не являются обязательными. Но при необходимости можно отправлять дополнительные параметры поставщику удостоверений. В следующем примере к запросу авторизации добавляется параметр строки запроса **domain_hint** со значением `contoso.com`.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список заявок, возвращенных поставщиком удостоверений OpenID Connect Connect. Может потребоваться сопоставить имя утверждения, определенное в вашей политике, с именем, определенным у поставщика удостоверений. Вы также можете добавить утверждения, которые не возвращаются поставщиком удостоверений, установив атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения выходных утверждений или создания новых.

В этом примере показаны утверждения, возвращаемые поставщиком удостоверений Microsoft Account:

- Подзапрос **,** сопоставленный с утверждением **issuerUserId** .
- утверждение **name**, которое сопоставляется с утверждением **displayName**;
- утверждение **email** без сопоставления с именем.

Технический профиль также возвращает утверждения, которые не возвращаются поставщиком удостоверений:

- утверждение **IdentityProvider**, содержащее имя поставщика удостоверений;
- утверждение **AuthenticationSource** со значением по умолчанию **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| client_id | ДА | Идентификатор приложения поставщика удостоверений. |
| IdTokenAudience | Нет | Аудитория id_token. Если этот параметр задан, Azure AD B2C проверяет, содержится ли маркер в утверждении, которое возвращает поставщик удостоверений, и соответствует ли маркер заданному параметру. |
| METADATA | ДА | URL-адрес, указывающий на документ конфигурации JSON, отформатированный в соответствии со спецификацией OpenID Connect Discovery, которая также известна как распространенная конечная точка конфигурации openid. |
| ProviderName | Нет | Имя поставщика удостоверений. |
| response_types | Нет | Тип ответа в соответствии со спецификацией OpenID Connect Core 1.0. Допустимые значения: `id_token`, `code` или `token`. |
| response_mode | Нет | Метод, который использует поставщик удостоверений, чтобы отправить результат обратно в Azure AD B2C. Возможные значения: `query`, `form_post` (по умолчанию) или `fragment`. |
| scope | Нет | Область запроса, определяемая в соответствии со спецификацией OpenID Connect Connect Core 1,0. Возможные значения: `openid`, `profile` и `email`. |
| HttpBinding | Нет | Ожидаемая привязка HTTP для маркера доступа и конечных точек маркера утверждений. Возможные значения: `GET` или `POST`.  |
| ValidTokenIssuerPrefixes | Нет | Ключ, который можно использовать для входа во все клиенты при использовании мультитенантного поставщика удостоверений, например Azure Active Directory. |
| UsePolicyInRedirectUri | Нет | Указывает, следует ли использовать политику при создании универсального кода ресурса (URI) перенаправления. При настройке приложения в поставщике удостоверений необходимо указать URI перенаправления. URI перенаправления указывает на Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Если вы указываете `false`, необходимо добавить URI перенаправления для каждой используемой политики. Например, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Нет | Указывает, должен ли запрос внешней службы помечаться как запрос, завершившийся сбоем, если код состояния Http находится в диапазоне 5xx. Значение по умолчанию — `false`. |
| DiscoverMetadataByTokenIssuer | Нет | Указывает, следует ли обнаруживать метаданные OIDC с помощью издателя в маркере JWT. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** содержит следующие атрибуты:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| client_secret | ДА | Секрет клиента от приложения поставщика удостоверений. Ключ шифрования является обязательным, только если для метаданных **response_types** задано значение `code`. В этом случае Azure AD B2C выполняет другой вызов для обмена кода авторизации на маркер доступа. Если для метаданных задано значение `id_token`, криптографический ключ можно не указывать.  |

## <a name="redirect-uri"></a>URI перенаправления

При настройке URI перенаправления поставщика удостоверений введите `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Обязательно замените `{your-tenant-name}` именем своего клиента. URI перенаправления должен содержать только строчные символы.

Примеры.

- [Azure Active Directory B2C. Добавление учетной записи Майкрософт (MSA) в качестве поставщика удостоверений с помощью пользовательских политик](active-directory-b2c-custom-setup-msa-idp.md)
- [Azure Active Directory B2C. Выполнение входа с помощью учетных записей Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Azure Active Directory B2C. Предоставление пользователям возможности входить в мультитенантный поставщик удостоверений Azure AD с помощью пользовательских политик](active-directory-b2c-setup-commonaad-custom.md)

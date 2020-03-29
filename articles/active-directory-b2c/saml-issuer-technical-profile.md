---
title: Определение технического профиля эмитента SAML в пользовательской политике
titleSuffix: Azure AD B2C
description: Определите технический профиль для маркера языка безопасности (SAML) в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967273"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Определите технический профиль для эмитента токенов SAML в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

При обработке каждого потока аутентификации Azure Active Directory B2C (Azure AD B2C) создает маркеры безопасности различных типов. Технический профиль эмитента токенов SAML излучает токен SAML, который возвращается обратно в приложение relying party (поставщик услуг). Как правило, этот технический профиль является последним шагом оркестрации на пути взаимодействия пользователя.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `None`. Для элемента **OutputTokenFormat** задайте значение `SAML2`.

В следующем примере показан технический профиль для `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Входные данные, выходные данные и сохранение утверждений

Элементы **InputClaims**, **OutputClaims** и **PersistClaims** являются пустыми или отсутствуют. Элементы **InutputClaimsTransformations** и **OutputClaimsTransformations** также отсутствуют.

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| IssuerUri | нет | Имя эмитента, которое отображается в ответе SAML. Значение должно быть таким же именем, как настроено в приложении для опирающихся сторон. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент CryptographicKeys содержит следующие атрибуты:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| MetadataSigning | Да | Сертификат X509 (набор ключей RSA), используемый для подписывания метаданных SAML. Azure AD B2C использует этот ключ для подписывания метаданных. |
| SamlMessageSigning| Да| Укажите сертификат X509 (набор ключей RSA) для подписания сообщений SAML. Azure AD B2C использует этот `<samlp:Response>` ключ для подписания ответа, отправляемого на сторону, полагающейся.|

## <a name="session-management"></a>Управление сеансом

Для настройки сеансов Azure AD B2C SAML между приложением `UseTechnicalProfileForSessionManagement` для полагающих сторон, атрибутом элемента, ссылкой на сессию [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO.

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующую статью, например, с использованием технического профиля эмитента SAML:

- [Регистрация приложения SAML в Azure AD B2C](connect-with-saml-service-providers.md)













---
title: Определение технического профиля для издателя SAML в пользовательской политике
titleSuffix: Azure AD B2C
description: Определите технический профиль для издателя токенов SAML (язык разметки заявлений системы безопасности) в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f7beccde92030d1e01633f4e4044849d7e91d05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229958"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля для издателя токенов SAML в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

При обработке каждого потока проверки подлинности Azure Active Directory B2C (Azure AD B2C) создает маркеры безопасности различных типов. Технический профиль для издателя токенов SAML выдает токен SAML, который возвращается в приложение проверяющей стороны (поставщик услуг). Как правило, этот технический профиль является последним шагом оркестрации на пути взаимодействия пользователя.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `None`. Для элемента **OutputTokenFormat** задайте значение `SAML2`.

В следующем примере показан технический профиль для `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
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
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Входные данные, выходные данные и сохранение утверждений

Элементы **InputClaims**, **OutputClaims** и **PersistClaims** являются пустыми или отсутствуют. Элементы **InutputClaimsTransformations** и **OutputClaimsTransformations** также отсутствуют.

## <a name="metadata"></a>Метаданные

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| IssuerUri | Нет | Имя издателя, которое отображается в ответе SAML. Значение должно совпадать с именем, настроенным в приложении проверяющей стороны. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент CryptographicKeys содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| MetadataSigning | Да | Сертификат X509 (набор ключей RSA), используемый для подписывания метаданных SAML. Azure AD B2C использует этот ключ для подписывания метаданных. |
| SamlMessageSigning| Да| Укажите сертификат X509 (набор ключей RSA), используемый для подписывания сообщений SAML. Azure AD B2C использует этот ключ для подписывания ответа `<samlp:Response>` и отправки его проверяющей стороне.|

## <a name="session-management"></a>Управление сеансом

Чтобы настроить сеансы SAML в Azure AD B2C между приложением проверяющей стороны и атрибутом элемента `UseTechnicalProfileForSessionManagement`, требуется ссылка на сеанс единого входа [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="next-steps"></a>Дальнейшие действия

Пример использования технического профиля издателя SAML см. в следующей статье:

- [Регистрация приложения SAML в Azure AD B2C](connect-with-saml-service-providers.md)













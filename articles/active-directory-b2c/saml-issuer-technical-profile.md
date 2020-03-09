---
title: Определение технического профиля для издателя SAML в настраиваемой политике
titleSuffix: Azure AD B2C
description: Определите технический профиль для издателя токена язык разметки зявлений системы безопасности (SAML) (SAML) в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f038868e80b600c805a1f33a54f9d0032e81dab8
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933197"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля для издателя маркера SAML в Azure Active Directory B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) выдает несколько типов маркеров безопасности при обработке каждого потока проверки подлинности. Технический профиль для издателя маркера SAML создает маркер SAML, возвращаемый обратно в приложение проверяющей стороны (поставщик услуг). Как правило, этот технический профиль является последним шагом оркестрации на пути взаимодействия пользователя.

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
| IssuerUri | Нет | Имя издателя, которое отображается в ответе SAML. Значение должно совпадать с именем, настроенным в приложении проверяющей стороны. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент CryptographicKeys содержит следующие атрибуты:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| MetadataSigning | Да | Сертификат X509 (набор ключей RSA), используемый для подписывания метаданных SAML. Azure AD B2C использует этот ключ для подписывания метаданных. |
| SamlMessageSigning| Да| Укажите сертификат X509 (набор ключей RSA), который будет использоваться для подписи сообщений SAML. Azure AD B2C использует этот ключ для подписи ответа `<samlp:Response>` отправки проверяющей стороне.|

## <a name="session-management"></a>Управление сеансом

Чтобы настроить сеансы SAML Azure AD B2C между приложением проверяющей стороны, атрибутом элемента `UseTechnicalProfileForSessionManagement` следует ссылка на сеанс единого входа [самлссосессионпровидер](custom-policy-reference-sso.md#samlssosessionprovider) .













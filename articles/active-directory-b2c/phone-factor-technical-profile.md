---
title: Определение технического профиля фактора телефона в пользовательской политике
titleSuffix: Azure AD B2C
description: Определите технический профиль фактора телефона в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332654"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определите технический профиль фактора телефона в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку регистрации и проверки телефонных номеров. Этот технический профиль:

- Предоставляет пользовательский интерфейс для взаимодействия с пользователем.
- Использует определение содержимого для управления внешний вид.
- Поддерживает как телефонные звонки, так и текстовые сообщения для проверки номера телефона.
- Поддерживает несколько телефонных номеров. Пользователь может выбрать один из телефонных номеров для проверки.  
- Если удается номер телефона, пользовательский интерфейс фактора телефона просит пользователя проверить номер телефона. Если он не предоставляется, он просит пользователя зарегистрировать новый номер телефона.
- Возвращает претензию с указанием того, предоставил ли пользователь новый номер телефона. Это утверждение можно использовать для решения о том, следует ли сохранять номер телефона в профиле пользователя Azure AD.  

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **обработчика** должен содержать полностью квалифицированное название сборки обработчика протоколов, которая используется Azure AD B2C для телефонного фактора:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Следующий пример показывает технический профиль фактора телефона для регистрации и проверки:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Входящие утверждения

Элемент Вхотисты должен содержать следующие требования. Вы также можете сопоставить имя вашей претензии к имени, определенному в техническом профиле фактора телефона. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

Следующий пример демонстрирует использование нескольких телефонных номеров. Для получения дополнительной [sample policy](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)информации см.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

Элемент InputClaimsTransformations может содержать коллекцию элементов InputClaimsTransformation, которые используются для изменения утверждений ввода или создания новых, прежде чем представить их на страницу фактора телефона.

## <a name="output-claims"></a>Исходящие утверждения

Элемент OutputClaims содержит список претензий, возвращенных техническим профилем фактора телефона.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

Элемент OutputClaimsTransformations может содержать коллекцию элементов OutputClaimsTransformation, которые используются для изменения исходящих утверждений или создания новых.

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** не используется.


## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Да | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим техническим профилем. |
| ManualPhoneNumberEntryРазрешено| нет | Укажите, разрешено ли пользователю вручную вводить номер телефона. Возможные `true` значения: `false` или (по умолчанию).|

### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Элементы пользовательского интерфейса страницы пользователя фактора телефона могут быть [локализованы.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Дальнейшие действия

- Проверьте [социальные и местные счета с](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) МИД стартовый пакет.


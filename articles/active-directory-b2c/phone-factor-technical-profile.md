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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437449"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определите технический профиль фактора телефона в пользовательской политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку регистрации и проверки телефонных номеров. Этот технический профиль:

- Предоставляет пользовательский интерфейс для взаимодействия с пользователем для проверки или регистрации номера телефона.
- Поддерживает телефонные звонки и текстовые сообщения для проверки номера телефона.
- Поддерживает несколько телефонных номеров. Пользователь может выбрать один из телефонных номеров для проверки.  
- Возвращает претензию с указанием того, предоставил ли пользователь новый номер телефона. Вы можете использовать это утверждение, чтобы решить, следует ли сохранять номер телефона в профиле пользователя Azure AD B2C.  
- Использует [определение содержимого](contentdefinitions.md) для управления внешний вид.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **обработчика** должен содержать полностью квалифицированное название сборки обработчика протоколов, которая используется Azure AD B2C для телефонного фактора:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Следующий пример показывает технический профиль фактора телефона для регистрации и проверки:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Преобразования входных требований

Элемент InputClaimsTransformations может содержать набор преобразований входных требований, которые используются для изменения требований ввода или создания новых. Следующая трансформация входных `UserId` требований генерирует претензию, которая используется позже в коллекции вхотливых требований.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Входящие утверждения

Элемент InputClaims должен содержать следующие претензии. Вы также можете сопоставить имя вашей претензии к имени, определенному в техническом профиле фактора телефона. 

|  Тип данных| Обязательно | Описание |
| --------- | -------- | ----------- | 
| строка| Да | Уникальный идентификатор пользователя. Имя претензии, или PartnerClaimType `UserId`должны быть установлены на . Это утверждение не должно содержать личную идентифицируемую информацию.|
| строка| Да | Список типов претензий. Каждая претензия содержит один номер телефона. Если какой-либо из требований о входе не содержит номера телефона, пользователю будет предложено зарегистрироваться и проверить новый номер телефона. Проверенный номер телефона возвращается как выходная претензия. Если одна из претензий ввода содержит номер телефона, пользователю предлагается проверить его. Если несколько утверждений о входе содержат номер телефона, пользователю предлагается выбрать и проверить один из номеров телефона. |

Следующий пример демонстрирует использование нескольких телефонных номеров. Для получения дополнительной [sample policy](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)информации см.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Исходящие утверждения

Элемент OutputClaims содержит список претензий, возвращенных техническим профилем фактора телефона.

|  Тип данных| Обязательно | Описание |
|  -------- | ----------- |----------- |
| Логическое | Да | Указывает, был ли введен пользователем новый номер телефона. Имя претензии или PartnerClaimType должны быть установлены на`newPhoneNumberEntered`|
| строка| Да | Проверенный номер телефона. Имя претензии, или PartnerClaimType `Verified.OfficePhone`должны быть установлены на .|

Элемент OutputClaimsTransformations может содержать коллекцию элементов OutputClaimsTransformation, которые используются для изменения требований вывода или создания новых.

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** не используется.


## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Да | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим техническим профилем. |
| ManualPhoneNumberEntryРазрешено| Нет | Укажите, разрешено ли пользователю вручную вводить номер телефона. Возможные значения: `true` `false` или (по умолчанию).|
| setting.authenticationMode | Нет | Метод проверки номера телефона. Возможные `sms`значения: `phone`, `mixed` или (по умолчанию).|
| setting.autodial| Нет| Укажите, должен ли технический профиль автоматически набирать или автоматически отправлять SMS. Возможные значения: `true` `false` или (по умолчанию). Автоциферный `setting.authenticationMode` набор требует метаданных быть `sms`установлены, или `phone`. Сбор входной связи должен иметь единый номер телефона. |

### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Элементы пользовательского интерфейса страницы пользователя фактора телефона могут быть [локализованы.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Следующие шаги

- Проверьте [социальные и местные счета с](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) МИД стартовый пакет.

---
title: Включить одноразовую проверку пароля (OTP)
titleSuffix: Azure AD B2C
description: Узнайте, как настроить одноразовый пароль (OTP) с помощью пользовательских политик Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332786"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение одноразового технического профиля пароля в пользовательской политике Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку управления генерацией и проверки одноразового пароля. Используйте технический профиль для создания кода, а затем проверьте этот код позже.

Технический профиль одноразового пароля также может вернуть сообщение об ошибке во время проверки кода. Дизайн интеграции с одноразовым паролем с помощью **технического профиля проверки.** Технический профиль проверки вызывает технический профиль одноразовых паролей для проверки кода. Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. С помощью технического профиля проверки сообщение об ошибке отображается на самоутверждается странице.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **обработчика** должен содержать полностью квалифицированное имя сборки обработчика протоколов, используемой Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

В следующем примере показан технический профиль одноразовых паролей:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Создание кода

Первый способ этого технического профиля заключается в генерации кода. Ниже приведены параметры, которые могут быть настроены для этого режима.

### <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список претензий, необходимых для отправки поставщику одноразовых протоколов паролей. Вы также можете сопоставить имя вашей претензии к названию, определенному ниже.

| ClaimReferenceId | Обязательно | Описание |
| --------- | -------- | ----------- |
| идентификатор | Да | Идентификатор для идентификации пользователя, которому необходимо проверить код позже. Он обычно используется в качестве идентификатора назначения, куда код доставляется, например, адрес электронной почты или номер телефона. |

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation,** которые используются для изменения утверждений о входе или создания новых перед отправкой поставщику одноразовых протоколов паролей.

### <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список претензий, генерируемых одноразовым поставщиком протоколов паролей. Вы также можете сопоставить имя вашей претензии к названию, определенному ниже.

| ClaimReferenceId | Обязательно | Описание |
| --------- | -------- | ----------- |
| otpGenerated | Да | Сгенерированный код, сеанс которого управляется Azure AD B2C. |

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

Для настройки режима генерации кода можно использовать следующие параметры:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| КодЭкспирацияInSeconds | нет | Время в секундах до истечения срока действия кода. Минимум: `60`; Максимум: `1200`; По `600`умолчанию: . |
| CodeLength | нет | Длина кода. Значение по умолчанию — `6`. |
| CharacterSet | нет | Набор символов для кода, отформатированный для использования в обычном выражении. Например, `a-z0-9A-Z`. Значение по умолчанию — `0-9`. Набор символов должен включать не менее 10 различных символов в указанном наборе. |
| NumRetryПопытки | нет | Количество попыток проверки до того, как код будет признан недействительным. Значение по умолчанию — `5`. |
| Операция | Да | Операция, которая выполняется. Возможная `GenerateCode`стоимость: . |
| Повторное использованиеSameCode | нет | Следует ли давать дубликат кода, а не создавать новый код, когда срок действия данного кода не истек и остается в силе. Значение по умолчанию — `false`. |

### <a name="example"></a>Пример

Для создания `TechnicalProfile` кода используется следующий пример:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Проверить код

Второй способ этого технического профиля заключается в проверке кода. Ниже приведены параметры, которые могут быть настроены для этого режима.

### <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список претензий, необходимых для отправки поставщику одноразовых протоколов паролей. Вы также можете сопоставить имя вашей претензии к названию, определенному ниже.

| ClaimReferenceId | Обязательно | Описание |
| --------- | -------- | ----------- |
| идентификатор | Да | Идентификатор для идентификации пользователя, который ранее сгенерировал код. Он обычно используется в качестве идентификатора назначения, куда код доставляется, например, адрес электронной почты или номер телефона. |
| OTpToVerify | Да | Код проверки, предоставленный пользователем. |

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation,** которые используются для изменения утверждений о входе или создания новых перед отправкой поставщику одноразовых протоколов паролей.

### <a name="output-claims"></a>Исходящие утверждения

В ходе проверки кода этого поставщика протоколов не предусмотрено никаких претензий к выводам.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

Следующие настройки можно использовать в режиме проверки кода:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Операция, которая выполняется. Возможная `VerifyCode`стоимость: . |


### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Следующие метаданные могут быть использованы для настройки сообщений об ошибках, отображаемых при сбое проверки кода. Метаданные должны быть настроены в [самоутверждаемом](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках могут быть [локализованы.](localization-string-ids.md#one-time-password-error-messages)

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | нет | Сообщение для отображения пользователю, если сессия проверки кода истек. Либо код истек, либо код никогда не генерировался для данного идентификатора. |
| UserMessageifMaxRetryAttempted | нет | Сообщение для отображения пользователю, если они превысили максимально допустимые попытки проверки. |
| UserMessageIfInvalidCode | нет | Сообщение для отображения пользователю, если он предоставил недействительный код. |
|UserMessageIfSessionConflict|нет| Сообщение для отображения пользователю, если код не может быть проверен.|

### <a name="example"></a>Пример

Для проверки кода используется следующий пример: `TechnicalProfile`

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующую статью, например, с использованием одноразового технического профиля пароля с пользовательской проверкой электронной почты:

- [Пользовательская проверка электронной почты в Azure Active Directory B2C](custom-email.md)


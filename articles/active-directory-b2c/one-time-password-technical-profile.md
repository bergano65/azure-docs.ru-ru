---
title: Включить проверку одноразового пароля (OTP)
titleSuffix: Azure AD B2C
description: Узнайте, как настроить сценарий одноразового пароля (OTP) с помощью Azure AD B2C пользовательских политик.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9becb91cfffd4553b2b8aa1a2d616963eae92ab0
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114050"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение технического профиля с одноразовым паролем в Azure AD B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку управления созданием и проверкой одноразового пароля. Используйте технический профиль для создания кода, а затем проверьте этот код позже.

Технический профиль одноразового пароля также может возвращать сообщение об ошибке во время проверки кода. Создайте интеграцию с одноразовым паролем с помощью **технического профиля проверки**. Технический профиль проверки вызывает одноразовый технический профиль пароля для проверки кода. Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. В техническом профиле проверки на странице с автоматическим подтверждением отображается сообщение об ошибке.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, используемой Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

В следующем примере показан технический профиль одноразового пароля:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Создание кода

Первый режим этого технического профиля — создание кода. Ниже приведены параметры, которые можно настроить для этого режима.

### <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений, необходимых для отправки поставщику одноразового пароля. Вы также можете сопоставлять имя утверждения с именем, определенным ниже.

| клаимреференцеид | Обязательно | Description |
| --------- | -------- | ----------- |
| идентификатор | Да | Идентификатор для идентификации пользователя, который должен проверить код позже. Он обычно используется в качестве идентификатора назначения, в которое доставляется код, например адрес электронной почты или номер телефона. |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или для создания новых перед отправкой поставщику протокола одноразовых паролей.

### <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список заявок, созданных поставщиком протокола одноразовых паролей. Вы также можете сопоставлять имя утверждения с именем, определенным ниже.

| клаимреференцеид | Обязательно | Description |
| --------- | -------- | ----------- |
| отпженератед | Да | Созданный код, сеанс которого управляется Azure AD B2C. |

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

Для настройки создания и обслуживания кода можно использовать следующие параметры.

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| кодикспиратионинсекондс | нет | Время в секундах до истечения срока действия кода. Минимум: `60`; Максимум: `1200`; Значение по умолчанию: `600`. |
| коделенгс | нет | Длина кода. Значение по умолчанию — `6`. |
| CharacterSet | нет | Кодировка для кода, отформатированная для использования в регулярном выражении. Например, `a-z0-9A-Z`. Значение по умолчанию — `0-9`. Кодировка должна содержать не менее 10 различных символов в указанном наборе. |
| нумретряттемптс | нет | Число попыток проверки, прежде чем код будет считаться недопустимым. Значение по умолчанию — `5`. |
| Операция | Да | Операция, которая выполняется. Возможные значения: `GenerateCode`или `VerifyCode`. |
| реусесамекоде | нет | Должно ли быть задано дублирование кода, а не создание нового кода, если срок действия данного кода не истек и он по-прежнему действителен. Значение по умолчанию — `false`. |

### <a name="returning-error-message"></a>Возвращаемое сообщение об ошибке

В режиме создания кода не возвращается сообщение об ошибке.

### <a name="example"></a>Пример

В следующем примере `TechnicalProfile` используется для создания кода:

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

Второй режим этого технического профиля — проверка кода. Ниже приведены параметры, которые можно настроить для этого режима.

### <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений, необходимых для отправки поставщику одноразового пароля. Вы также можете сопоставлять имя утверждения с именем, определенным ниже.

| клаимреференцеид | Обязательно | Description |
| --------- | -------- | ----------- |
| идентификатор | Да | Идентификатор для идентификации пользователя, который ранее сгенерировал код. Он обычно используется в качестве идентификатора назначения, в которое доставляется код, например адрес электронной почты или номер телефона. |
| отптоверифи | Да | Код проверки, предоставленный пользователем. |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или для создания новых перед отправкой поставщику протокола одноразовых паролей.

### <a name="output-claims"></a>Исходящие утверждения

При проверке кода этого поставщика протокола не предоставлены выходные утверждения.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

Следующие параметры можно использовать для настройки сообщения об ошибке, отображаемого при сбое проверки кода.

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| усермессажеифсессиондоеснотексист | нет | Сообщение, отображаемое пользователю, если истек срок действия сеанса проверки кода. Это либо срок действия кода, либо код, который никогда не был создан для данного идентификатора. |
| усермессажеифмаксретряттемптед | нет | Сообщение, отображаемое пользователю, если превышено максимально допустимое количество попыток проверки. |
| усермессажеифинвалидкоде | нет | Сообщение, отображаемое пользователю, если он предоставил недопустимый код. |

### <a name="returning-error-message"></a>Возвращаемое сообщение об ошибке

Как описано в статье [метаданные](#metadata), можно настроить сообщение об ошибке, отображаемое пользователю для различных вариантов ошибок. Вы можете дополнительно локализовать эти сообщения, добавив префикс в языковой стандарт, например:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Пример

В следующем примере `TechnicalProfile` используется для проверки кода:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Дальнейшие действия

В следующей статье приведены примеры использования одноразового профиля технические Password с пользовательской проверкой электронной почты.

- [Настраиваемая проверка электронной почты в Azure Active Directory B2C](custom-email.md)


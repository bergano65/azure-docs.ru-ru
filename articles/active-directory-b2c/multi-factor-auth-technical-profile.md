---
title: Технические профили Azure MFA в пользовательских политиках
titleSuffix: Azure AD B2C
description: Справочник по настраиваемой политике для технических профилей Azure многофакторной идентификации (MFA) в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184287"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение технического профиля Azure MFA в Azure AD B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку проверки номера телефона с помощью многофакторной идентификации Azure (MFA). Используйте этот технический профиль, чтобы создать и отправить код на номер телефона, а затем проверить код.

Технический профиль Azure MFA также может возвращать сообщение об ошибке. Вы можете спроектировать интеграцию с Azure MFA с помощью **технического профиля проверки**. Технический профиль проверки вызывает службу Azure MFA. Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. В техническом профиле проверки отображается сообщение об ошибке на странице с автоматическим подтверждением.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, используемой Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

В следующем примере показан технический профиль Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Отправить SMS

Первый режим этого технического профиля — создание кода и его отправка. Для этого режима можно настроить следующие параметры.

### <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений для отправки в Azure mfa. Вы также можете сопоставлять имя заявки с именем, определенным в техническом профиле MFA.

| клаимреференцеид | Обязательно | Description |
| --------- | -------- | ----------- |
| userPrincipalName | Да | Идентификатор пользователя, владеющего номером телефона. |
| phoneNumber | Да | Номер телефона, по которому отправляется код SMS. |
| companyName | нет |Название компании в SMS. Если этот параметр не указан, используется имя приложения. |
| локаль | нет | Языковой стандарт SMS. Если он не указан, используется языковой стандарт браузера. |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или создания новых перед отправкой в службу Azure mfa.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протокола Azure MFA не возвращает **OutputClaims**, поэтому нет необходимости указывать выходные утверждения. Однако можно включить утверждения, которые не возвращаются поставщиком удостоверений Azure MFA, при условии, что задан атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| Операция | Да | Должен быть **оневайсмс**.  |
| усермессажеифинвалидформат | нет | Пользовательское сообщение об ошибке, если указанный номер телефона не является допустимым номером телефона |
| усермессажеифкаулднтсендсмс | нет | Пользовательское сообщение об ошибке, если указанный номер телефона не принимает SMS |
| усермессажеифсервереррор | нет | Пользовательское сообщение об ошибке, если на сервере произошла внутренняя ошибка |

### <a name="return-an-error-message"></a>Возврат сообщения об ошибке

Как описано в статье [метаданные](#metadata), можно настроить сообщение об ошибке, отображаемое пользователю для различных вариантов ошибок. Дальнейшую локализацию этих сообщений можно выполнить с помощью префикса языкового стандарта. Пример:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Пример. Отправка SMS

В следующем примере показан технический профиль Azure MFA, который используется для отправки кода через SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Проверить код

Второй режим этого технического профиля — проверка кода. Для этого режима можно настроить следующие параметры.

### <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений для отправки в Azure mfa. Вы также можете сопоставлять имя заявки с именем, определенным в техническом профиле MFA.

| клаимреференцеид | Обязательно | Description |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Да | Тот же номер телефона, который использовался ранее для отправки кода. Он также используется для нахождение сеанса проверки телефона. |
| верификатионкоде  | Да | Код проверки, предоставленный пользователем для проверки |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или создания новых перед вызовом службы Azure mfa.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протокола Azure MFA не возвращает **OutputClaims**, поэтому нет необходимости указывать выходные утверждения. Однако можно включить утверждения, которые не возвращаются поставщиком удостоверений Azure MFA, при условии, что задан атрибут `DefaultValue`.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

## <a name="metadata"></a>Метаданные

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| Операция | Да | Необходимо **проверить** |
| усермессажеифинвалидформат | нет | Пользовательское сообщение об ошибке, если указанный номер телефона не является допустимым номером телефона |
| усермессажеифвронгкодинтеред | нет | Настраиваемое сообщение об ошибке, если код, указанный для проверки, неверен |
| усермессажеифмаксалловедкодеретриреачед | нет | Пользовательское сообщение об ошибке, если пользователь попытался слишком много раз проверить код проверки |
| усермессажеифсроттлед | нет | Настраиваемое сообщение об ошибке, если пользователь регулируется |
| усермессажеифсервереррор | нет | Пользовательское сообщение об ошибке, если на сервере произошла внутренняя ошибка |

### <a name="return-an-error-message"></a>Возврат сообщения об ошибке

Как описано в статье [метаданные](#metadata), можно настроить сообщение об ошибке, отображаемое пользователю для различных вариантов ошибок. Дальнейшую локализацию этих сообщений можно выполнить с помощью префикса языкового стандарта. Пример:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Пример: Проверка кода

В следующем примере показан технический профиль Azure MFA, используемый для проверки кода.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```

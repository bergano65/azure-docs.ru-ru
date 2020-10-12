---
title: Технические профили Azure MFA в пользовательских политиках
titleSuffix: Azure AD B2C
description: Справочник по настраиваемой политике для технических профилей Azure многофакторной идентификации (MFA) в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 71040f831ed7a64f2bc7be7f3a75218976fc2559
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385949"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение технического профиля Azure MFA в Azure AD B2C настраиваемой политике

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) обеспечивает поддержку проверки номера телефона с помощью многофакторной идентификации Azure (MFA). Используйте этот технический профиль, чтобы создать и отправить код на номер телефона, а затем проверить код. Технический профиль Azure MFA также может возвращать сообщение об ошибке.  Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. В техническом профиле проверки отображается сообщение об ошибке на странице с автоматическим подтверждением.

Этот технический профиль:

- Не предоставляет интерфейс для взаимодействия с пользователем. Вместо этого пользовательский интерфейс вызывается из [самостоятельно утвержденного](self-asserted-technical-profile.md) технического профиля или [элемента управления отображением](display-controls.md) в качестве [технического профиля проверки](validation-technical-profile.md).
- Использует службу Azure MFA для создания и отправки кода на номер телефона, а затем проверяет код.  
- Проверяет номер телефона через текстовые сообщения.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, используемой Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

В следующем примере показан технический профиль Azure MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Краткое руководство по отправке SMS-сообщений

Первый режим этого технического профиля — создание кода и его отправка. Для этого режима можно настроить следующие параметры.

### <a name="input-claims"></a>Входящие утверждения

Элемент **inputclaim** содержит список утверждений для отправки в Azure mfa. Вы также можете сопоставлять имя заявки с именем, определенным в техническом профиле MFA.

| клаимреференцеид | Обязательно | Описание |
| --------- | -------- | ----------- |
| userPrincipalName | Да | Идентификатор пользователя, владеющего номером телефона. |
| phoneNumber | Да | Номер телефона, по которому отправляется код SMS. |
| companyName | Нет |Название компании в SMS. Если этот параметр не указан, используется имя приложения. |
| locale | Нет | Языковой стандарт SMS. Если он не указан, используется языковой стандарт браузера. |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или создания новых перед отправкой в службу Azure mfa.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протокола Azure MFA не возвращает **OutputClaims**, поэтому нет необходимости указывать выходные утверждения. Однако можно включить утверждения, которые не возвращаются поставщиком удостоверений Azure MFA, при условии, что задан `DefaultValue` атрибут.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Должен быть **оневайсмс**.  |

#### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Следующие метаданные можно использовать для настройки сообщений об ошибках, отображаемых при отправке ошибки SMS. Метаданные должны быть настроены в [самостоятельно утвержденном](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках можно [локализовать](localization-string-ids.md#azure-mfa-error-messages).

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Нет | Сообщение об ошибке пользователя, если указанный номер телефона не принимает SMS. |
| UserMessageIfInvalidFormat | Нет | Сообщение об ошибке пользователя, если указанный номер телефона не является допустимым номером телефона. |
| UserMessageIfServerError | Нет | Сообщение об ошибке пользователя, если на сервере произошла внутренняя ошибка. |
| UserMessageIfThrottled| Нет | Сообщение об ошибке пользователя, если запрос был отрегулирован.|

### <a name="example-send-an-sms"></a>Пример. Отправка SMS

В следующем примере показан технический профиль Azure MFA, который используется для отправки кода через SMS.

```xml
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

| клаимреференцеид | Обязательно | Описание |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Да | Тот же номер телефона, который использовался ранее для отправки кода. Он также используется для нахождение сеанса проверки телефона. |
| верификатионкоде  | Да | Код проверки, предоставленный пользователем для проверки |

Элемент **инпутклаимстрансформатионс** может содержать коллекцию элементов **инпутклаимстрансформатион** , которые используются для изменения входных утверждений или создания новых перед вызовом службы Azure mfa.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протокола Azure MFA не возвращает **OutputClaims**, поэтому нет необходимости указывать выходные утверждения. Однако можно включить утверждения, которые не возвращаются поставщиком удостоверений Azure MFA, при условии, что задан `DefaultValue` атрибут.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Необходимо **проверить** |

#### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Следующие метаданные можно использовать для настройки сообщений об ошибках, отображаемых при сбое проверки кода. Метаданные должны быть настроены в [самостоятельно утвержденном](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках можно [локализовать](localization-string-ids.md#azure-mfa-error-messages).

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Нет | Сообщение об ошибке пользователя, если пользователь попытался слишком много раз выполнить проверку кода. |
| UserMessageIfServerError | Нет | Сообщение об ошибке пользователя, если на сервере произошла внутренняя ошибка. |
| UserMessageIfThrottled| Нет | Сообщение об ошибке пользователя, если запрос регулируется.|
| UserMessageIfWrongCodeEntered| Нет| Сообщение об ошибке пользователя, если код, указанный для проверки, неверен.|

### <a name="example-verify-a-code"></a>Пример: Проверка кода

В следующем примере показан технический профиль Azure MFA, используемый для проверки кода.

```xml
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

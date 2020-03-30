---
title: Технические профили Azure MFA в пользовательских политиках
titleSuffix: Azure AD B2C
description: Ссылка на пользовательские политики для технических профилей Azure Multi-Factor Authentication (MFA) в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332804"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Определение технического профиля Azure MFA в пользовательской политике Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Активный каталог Azure B2C (Azure AD B2C) обеспечивает поддержку проверки номера телефона с помощью Azure Multi-Factor Authentication (MFA). Используйте этот технический профиль для создания и отправки кода на номер телефона, а затем проверяйте код. Технический профиль Azure MFA также может вернуть сообщение об ошибке.  Этот профиль проверяет данные, предоставленные пользователем, прежде чем продолжать путь взаимодействия пользователя. С помощью технического профиля проверки сообщение об ошибке отображается на самоутверждается странице.

Этот технический профиль:

- Не предоставляет интерфейс для взаимодействия с пользователем. Вместо этого пользовательский интерфейс вызывается из [самоутверждаемого](self-asserted-technical-profile.md) технического профиля или [управления дисплеем](display-controls.md) в качестве [технического профиля проверки.](validation-technical-profile.md)
- Использует службу Azure MFA для генерации и отправки кода на номер телефона, а затем проверяет код.  
- Проверяет номер телефона с помощью текстовых сообщений.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **обработчика** должен содержать полностью квалифицированное имя сборки обработчика протоколов, используемой Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

В следующем примере показан технический профиль МидФа Azure:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Отправить SMS

Первый способ этого технического профиля заключается в генерации кода и отправке его. Для этого режима можно настроить следующие параметры.

### <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список претензий для отправки в Azure MFA. Вы также можете сопоставить название вашей претензии на имя, определенное в техническом профиле МИД.

| ClaimReferenceId | Обязательно | Описание |
| --------- | -------- | ----------- |
| userPrincipalName | Да | Идентификатор пользователя, которому принадлежит номер телефона. |
| phoneNumber | Да | Номер телефона для отправки SMS-кода. |
| companyName | нет |Название компании в SMS. Если это не уведомляется, используется название приложения. |
| локаль | нет | Локаль SMS. Если пользователь не предоставлен, используется локал браузера пользователя. |

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation,** которые используются для изменения требований ввода или создания новых перед отправкой в службу Azure MFA.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протоколов Azure MFA не возвращает никаких **выходных требований,** поэтому нет необходимости указывать выходные претензии. Однако можно включить утверждения, которые не возвращаются поставщиком идентификационных данных `DefaultValue` Azure MFA до тех пор, пока вы установите атрибут.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Должно быть **OneWaySMS**.  |

#### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Следующие метаданные могут быть использованы для настройки сообщений об ошибках, отображаемых при отправке СмС-сбоя. Метаданные должны быть настроены в [самоутверждаемом](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках могут быть [локализованы.](localization-string-ids.md#azure-mfa-error-messages)

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | нет | Сообщение об ошибке пользователя, если предоставленный номер телефона не принимает SMS. |
| UserMessageIfInvalidFormat | нет | Сообщение об ошибке пользователя, если номер телефона не является действительным номером телефона. |
| UserMessageIfServerОшибка | нет | Сообщение об ошибке пользователя, если сервер столкнулся с внутренней ошибкой. |
| UserMessageIfthrottled| нет | Сообщение об ошибке пользователя, если запрос был задушен.|

### <a name="example-send-an-sms"></a>Пример: отправить SMS

В следующем примере показан технический профиль Azure MFA, который используется для отправки кода с помощью SMS.

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

Второй способ этого технического профиля заключается в проверке кода. Для этого режима можно настроить следующие параметры.

### <a name="input-claims"></a>Входящие утверждения

Элемент **InputClaims** содержит список претензий для отправки в Azure MFA. Вы также можете сопоставить название вашей претензии на имя, определенное в техническом профиле МИД.

| ClaimReferenceId | Обязательно | Описание |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Да | Тот же номер телефона, что и ранее используемый для отправки кода. Он также используется для поиска сеанса проверки телефона. |
| код проверки  | Да | Код проверки, предоставленный пользователем для проверки |

Элемент **InputClaimsTransformations** может содержать коллекцию элементов **InputClaimsTransformation,** которые используются для изменения требований ввода или создания новых перед вызовом службы Azure MFA.

### <a name="output-claims"></a>Исходящие утверждения

Поставщик протоколов Azure MFA не возвращает никаких **выходных требований,** поэтому нет необходимости указывать выходные претензии. Однако можно включить утверждения, которые не возвращаются поставщиком идентификационных данных `DefaultValue` Azure MFA до тех пор, пока вы установите атрибут.

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| Операция | Да | Должно быть **проверено** |

#### <a name="ui-elements"></a>Элементы пользовательского интерфейса

Следующие метаданные могут быть использованы для настройки сообщений об ошибках, отображаемых при сбое проверки кода. Метаданные должны быть настроены в [самоутверждаемом](self-asserted-technical-profile.md) техническом профиле. Сообщения об ошибках могут быть [локализованы.](localization-string-ids.md#azure-mfa-error-messages)

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| UserMessageifmaxAllowedCodeRere| нет | Сообщение об ошибке пользователя, если пользователь пытался код проверки слишком много раз. |
| UserMessageIfServerОшибка | нет | Сообщение об ошибке пользователя, если сервер столкнулся с внутренней ошибкой. |
| UserMessageIfthrottled| нет | Сообщение об ошибке пользователя, если запрос задушен.|
| UserMessageIfWrongCodeEntered| нет| Сообщение об ошибке пользователя, если код, введенный для проверки, неверен.|

### <a name="example-verify-a-code"></a>Пример: проверить код

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

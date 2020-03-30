---
title: Проверка утверждений с помощью элементов управления дисплеем
titleSuffix: Azure AD B2C
description: Узнайте, как использовать элементы управления отображением Azure AD B2C для проверки утверждений в пользовательских поездках, предусмотренных пользовательскими политиками.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188789"
---
# <a name="verification-display-control"></a>Контроль отсеиваем проверки

Используйте [элемент управления отображением](display-controls.md) проверки для проверки претензии, например, адреса электронной почты или номера телефона, с кодом проверки, отправленным пользователю.

## <a name="verificationcontrol-actions"></a>Действия ControlControl

Контроль отсеивания проверки состоит из двух этапов (действий):

1. Запросите пункт назначения у пользователя, например адрес электронной почты или номер телефона, на который должен быть отправлен код проверки. Когда пользователь выбирает кнопку **Send Code,** выполняется **действие SendCode** управления дисплеем проверки. **Действие SendCode** генерирует код, создает содержимое, подносивее, и отправляет его пользователю. Значение адреса может быть предварительно заселено и служить в качестве второго фактора аутентификации.

    ![Пример страницы для отправки кода](media/display-control-verification/display-control-verification-email-action-01.png)

1. После отправки кода пользователь читает сообщение, вводит код проверки в элемент управления, предоставляемый системой управления дисплеем, и выбирает **Код проверки.** Выбрав **Код проверки,** **действие VerifyCode** выполняется для проверки кода, связанного с адресом. Если пользователь выбирает **Отправить новый код,** первое действие выполняется снова.

    ![Пример страницы для проверки действия кода](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Control требует элементов

**Контроль проверки** должен содержать следующие элементы:

- Тип `DisplayControl` является `VerificationControl`.
- `DisplayClaims`
  - **Отправить** на - Один или несколько претензий с указанием, куда отправить код проверки. Например, *код электронной почты* или *страны* и *номер телефона.*
  - **Код проверки** - Код проверки утверждает, что пользователь предоставляет после того, как код был отправлен. Эта претензия должна быть установлена `ControlClaimType` по мере `VerificationCode`необходимости, и должна быть установлена на .
- Претензия на вывод (необязательная) должна быть возвращена на самоутверждаемую страницу после завершения пользователем процесса проверки. Например, *код электронной почты* или *страны* и *номер телефона.* Самоутверждаемый технический профиль использует претензии для сохранения данных или пузыря до вывода претензий к следующему этапу оркестровки.
- Два `Action`с со следующими именами:
  - **SendCode** - отправляет код пользователю. Это действие обычно содержит два технических профиля проверки, для создания кода и его отправки.
  - **VerifyCode** - проверяет код. Это действие обычно содержит один технический профиль проверки.

В приведенном ниже примере на странице отображается текстовый ящик **электронной почты.** Когда пользователь вводит свой адрес электронной почты и выбирает **SendCode,** действие **SendCode** срабатывает в задней части Azure AD B2C.

Затем пользователь вводит **код проверки** и выбирает **VerifyCode,** чтобы вызвать действие **VerifyCode** в задней части. Если все проверки проходят, **контроль проверки** считается полным и пользователь может продолжить следующий шаг.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

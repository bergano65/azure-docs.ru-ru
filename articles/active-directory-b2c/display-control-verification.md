---
title: Проверка утверждений с помощью элементов управления отображением
titleSuffix: Azure AD B2C
description: Узнайте, как использовать Azure AD B2C отображения элементов управления для проверки утверждений в пути взаимодействия пользователя, предоставленных пользовательскими политиками.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e42c889277f937a33e72eaf57819385166d6a409
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85202320"
---
# <a name="verification-display-control"></a>Контроль просмотра проверки

Используйте [контрольный элемент контроля](display-controls.md) для проверки утверждения, например адрес электронной почты или номер телефона, с кодом проверки, отправленным пользователю.

## <a name="verificationcontrol-actions"></a>Действия Верификатионконтрол

Элемент управления отображением проверки состоит из двух этапов (действий):

1. Запросите целевой объект от пользователя, например адрес электронной почты или номер телефона, на который должен быть отправлен код проверки. Когда пользователь нажимает кнопку **отправить код** , выполняется **действие SendCode** элемента управления отображением проверки. **Действие SendCode** создает код, создает содержимое для отправки и отправляет его пользователю. Значение адреса можно предварительно заполнить и использовать в качестве второго фактора проверки подлинности.

    ![Пример страницы для действия "Отправка кода"](media/display-control-verification/display-control-verification-email-action-01.png)

1. После отправки кода пользователь считывает сообщение, вводит код проверки в элемент управления, предоставленный элементом управления отображением, и выбирает **проверку кода**. Выбрав **проверить код**, выполняется **действие VerifyCode** для проверки кода, связанного с адресом. Если пользователь выбирает команду " **отправить новый код**", первое действие выполняется снова.

    ![Пример страницы для действия "Проверка кода"](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Обязательные элементы Верификатионконтрол

**Верификатионконтрол** должен содержать следующие элементы:

- Тип `DisplayControl` — `VerificationControl` .
- `DisplayClaims`
  - **Отправить в** один или несколько утверждений, указывающих, куда отправить код проверки. Например, *адрес электронной почты* или *код страны* и *номер телефона*.
  - **Код проверки** — утверждение проверочного кода, предоставленное пользователем после отправки кода. Это утверждение должно быть задано как обязательное, а значение `ControlClaimType` должно быть равно `VerificationCode` .
- Выходное утверждение (необязательно), возвращаемое на страницу с самоподтверждением после завершения пользователем процесса проверки. Например, *адрес электронной почты* или *код страны* и *номер телефона*. В самостоятельно утвержденном техническом профиле используются утверждения для сохранения данных или пузырьковой передачи выходных заявок на следующий этап оркестрации.
- Два `Action` s со следующими именами:
  - **SendCode** — отправляет пользователю код. Это действие обычно содержит два технического профиля проверки, чтобы создать код и отправить его.
  - **VerifyCode** — проверяет код. Это действие обычно содержит один технический профиль проверки.

В приведенном ниже примере на странице отображается текстовое поле **электронной почты** . Когда пользователь вводит свой адрес электронной почты и выбирает **SendCode**, действие **SendCode** активируется в Azure AD B2C серверной части.

Затем пользователь вводит **верификатионкоде** и выбирает **VerifyCode** , чтобы активировать действие **VerifyCode** в серверной части. Если все проверки пройдены, **верификатионконтрол** считается завершенным, и пользователь может перейти к следующему шагу.

```xml
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

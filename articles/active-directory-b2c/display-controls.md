---
title: Ссылка управления отображением
titleSuffix: Azure AD B2C
description: Ссылка на элементы управления дисплеем Azure AD B2C. Используйте элементы управления дисплеем для настройки пользовательских поездок, определенных в пользовательских политиках.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188738"
---
# <a name="display-controls"></a>Элементы управления отображением

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Элемент отображения** — это элемент пользовательского интерфейса, который имеет специальную функциональность и взаимодействует с бэк-эндом Службы Azure Active Directory B2C (Azure AD B2C). Это позволяет пользователю выполнять действия на странице, которые вызывают [технический профиль проверки](validation-technical-profile.md) в задней части. Элементы управления дисплея отображаются на странице и ссылаются на [самоутверждаемый технический профиль.](self-asserted-technical-profile.md)

Следующее изображение иллюстрирует самоутвержденную страницу регистрации с двумя элементами управления дисплеем, которые проверяют основной и вторичный адрес электронной почты.

![Пример отображения элемента управления дисплеем](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

 В разделе [Metadata](self-asserted-technical-profile.md#metadata) [самоутвержденного технического профиля,](self-asserted-technical-profile.md)ссылка [ContentDefinition](contentdefinitions.md) должна установить `DataUri` на странице контракт версии 2.0.0 или выше. Пример:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Определение элементов управления дисплеем

Элемент **DisplayControl** содержит следующие атрибуты:

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор, используемый для управления дисплеем. На него можно [ссылаться.](#referencing-display-controls) |
| UserInterfaceControlType | Да | Тип управления дисплеем. В настоящее время поддерживается [VerificationControl](display-control-verification.md) |

Элемент **DisplayControl** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** используются для предварительного заполнения значения претензий, которые будут собраны с пользователя. |
| DisplayClaims | 0:1 | **DisplayClaims** используются для представления претензий, которые должны быть собраны с пользователя. |
| OutputClaims | 0:1 | **OutputClaims** используются для представления претензий, которые будут временно сохранены для этого **DisplayControl.** |
| Действия | 0:1 | **Действия** используются для рассылки технических профилей проверки для использования действий пользователя, происходящих на переднем конце. |

### <a name="input-claims"></a>Входящие утверждения

При управлении дисплеем можно использовать элементы **InputClaims** для предварительного заполнения значения требований, которые можно получить от пользователя на странице. Любые **ВходныеПретензиИПреобразования** могут быть определены в самоутвержденном техническом профиле, который ссылается на этот элемент управления дисплеем.

Следующий пример преднаселяет адрес электронной почты, который должен быть проверен с уже присутствующим адресом.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Отображение претензий

Каждый тип управления дисплеем требует различных требований отображения, [притязаний](#output-claims)на вывод и [выполняемых действий.](#display-control-actions)

Подобно **претензиям дисплея,** определенным в [самоутвержденном техническом профиле,](self-asserted-technical-profile.md#display-claims)претензии дисплея представляют претензии, которые должны быть собраны с пользователя в элементе управления дисплеем. Элемент **ClaimType,** на который ссылается, должен указывать элемент **UserInputType** для ввода пользователя, `TextBox` `DropdownSingleSelect`поддерживаемый Azure AD B2C, например, или Если значение требования отображения требуется **действием,** установите **требуемый** атрибут, чтобы `true` заставить пользователя предоставить значение для этой конкретной претензии дисплея.

Для определенных типов управления дисплеем требуются определенные требования отображения. Например, для управления дисплеем type **VerificationControl**требуется **VerificationCode.** Используйте атрибут **ControlClaimType,** чтобы указать, какой DisplayClaim предназначен для этой требуемой претензии. Пример:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Исходящие утверждения

Выводные **претензии** управления дисплеем не отправляются на следующий этап оркестровки. Они временно сохраняются только для текущего сеанса управления дисплеем. Эти временные претензии могут быть разделены между различными действиями одного и того же элемента управления дисплеем.

Чтобы выпуклет выводные претензии к следующему этапу оркестровки, используйте **OutputClaims** фактического самоутвержденного технического профиля, который ссылается на этот элемент управления дисплеем.

### <a name="display-control-actions"></a>Действия управления отображением

**Действия** управления дисплеем — это процедуры, возникающие в задней части Azure AD B2C, когда пользователь выполняет определенное действие на стороне клиента (браузера). Например, проверка для выполнения, когда пользователь выбирает кнопку на странице.

Действие определяет список **технических профилей проверки.** Они используются для проверки некоторых или всех требований дисплея управления дисплеем. Технический профиль проверки проверяет ввод пользователя и может вернуть ошибку пользователю. Вы можете использовать **ContinueOnError,** **ContinueOnSuccess**и **предварительные условия** в действии управления дисплеем, аналогично тому, как они используются в [технических профилях проверки](validation-technical-profile.md) в самоутвержденном техническом профиле.

Следующий пример отправляет код либо по электронной почте, либо по SMS на основе выбора пользователем претензии **mfaType.**

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Элементы управления отображением ссылок

Элементы управления дисплеями упоминаются в [претензиях ототока](self-asserted-technical-profile.md#display-claims) [самоутвержденного технического профиля.](self-asserted-technical-profile.md)

Пример:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

---
title: Ссылка на элемент управления отображением
titleSuffix: Azure AD B2C
description: Справочник по элементам управления отображением Azure AD B2C. Используйте элементы управления отображением для настройки пути взаимодействия пользователя, определенного в пользовательских политиках.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 131ecd010cba55f08199f713654792c0844a47e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202302"
---
# <a name="display-controls"></a>Элементы управления отображением

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Элемент управления "Отображение** " — это элемент пользовательского интерфейса, который имеет специальные функции и взаимодействует с серверной службой Azure Active Directory B2C (Azure AD B2C). Она позволяет пользователю выполнять действия на странице, которая вызывает [технический профиль проверки](validation-technical-profile.md) на серверной части. Элементы управления отображением отображаются на странице, на которые ссылается [самостоятельно утвержденный технический профиль](self-asserted-technical-profile.md).

На следующем рисунке показана самоподтвержденная страница регистрации с двумя элементами управления отображением, проверяющими основной и дополнительный адреса электронной почты.

![Пример отображаемого элемента управления отображением](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

 В разделе [метаданных](self-asserted-technical-profile.md#metadata) [самостоятельно утвержденного технического профиля](self-asserted-technical-profile.md) [контентдефинитион](contentdefinitions.md) должен иметь `DataUri` значение контракта страницы Version 2.0.0 или выше. Пример:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Определение элементов управления отображением

Элемент **DisplayControl** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор, используемый для элемента управления отображением. На него можно [ссылаться](#referencing-display-controls). |
| усеринтерфацеконтролтипе | Да | Тип элемента управления отображением. В настоящее время поддерживается [верификатионконтрол](display-control-verification.md) |

Элемент **DisplayControl** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **Inputclaim** используются для предварительного заполнения значений утверждений, собираемых от пользователя. |
| дисплайклаимс | 0:1 | **Дисплайклаимс** используются для представления утверждений, собираемых от пользователя. |
| OutputClaims | 0:1 | **OutputClaims** используются для представления утверждений, которые должны быть временно сохранены для этого элемента **DisplayControl**. |
| Действия | 0:1 | **Действия** используются для перечисления технических профилей проверки для вызова действий пользователя, происходящих во внешнем интерфейсе. |

### <a name="input-claims"></a>Входящие утверждения

В элементе управления отображением можно использовать элементы **inputclaim** для предварительного заполнения значения заявок, которые будут собраны от пользователя на странице. Любой **инпутклаимстрансформатионс** может быть определен в самостоятельно утвержденном техническом профиле, который ссылается на этот элемент управления отображением.

В следующем примере предварительно заполняется адрес электронной почты, который должен быть проверен с помощью уже существующего адреса.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Отображение утверждений

Для каждого типа элементов управления отображением требуется другой набор утверждений, утверждений [вывода](#output-claims)и [действий](#display-control-actions) , которые должны быть выполнены.

Как и в случае с **утверждениями** , определенными в [самостоятельно утвержденном техническом профиле](self-asserted-technical-profile.md#display-claims), заявки на отображение представляют утверждения, которые должны быть собраны от пользователя в элементе управления отображением. Элементу, на который указывает **ссылка, необходимо** указать элемент **усеринпуттипе** для пользовательского типа ввода, поддерживаемого Azure AD B2C, например `TextBox` или `DropdownSingleSelect` . Если для **действия**требуется значение утверждения, задайте для атрибута **обязательный** атрибут `true` значение, чтобы пользователь предоставил для этого конкретного утверждения.

Для определенных типов элементов управления отображением требуются определенные утверждения на отображение. Например, **верификатионкоде** требуется для элемента управления отображением типа **верификатионконтрол**. Используйте атрибут **контролклаимтипе** , чтобы указать, какой дисплайклаим назначен для этого обязательного утверждения. Пример:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Исходящие утверждения

**Выходные утверждения** элемента управления отображением не отправляются на следующий этап оркестрации. Они сохраняются временно только для текущего сеанса управления отображением. Эти временные утверждения могут совместно использоваться разными действиями одного элемента управления отображением.

Чтобы перефильтровать выходные данные на следующий этап оркестрации, используйте **OutputClaims** фактического самопроверочного технического профиля, который ссылается на этот элемент управления отображением.

### <a name="display-control-actions"></a>Отображение действий элемента управления

**Действия** элемента управления отображением являются процедурами, происходящими в Azure AD B2C серверной части, когда пользователь выполняет определенное действие на стороне клиента (браузер). Например, проверки, выполняемые, когда пользователь выбирает кнопку на странице.

Действие определяет список **технических профилей проверки**. Они используются для проверки некоторых или всех утверждений, отображаемых элементом управления отображением. Технический профиль проверки проверяет введенные пользователем данные и может вернуть пользователю сообщение об ошибке. Можно использовать **ContinueOnError**, **континуеонсукцесс**и **предусловия** в действии элемента управления отображением аналогично тому, как они используются в [технических профилях проверки](validation-technical-profile.md) в самостоятельно утвержденном техническом профиле.

В следующем примере код отправляется в сообщении электронной почты или в SMS на основе выбранного пользователем утверждения **мфатипе** .

```xml
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

## <a name="referencing-display-controls"></a>Ссылки на элементы управления отображением

Ссылки на элементы управления отображением отображаются в [утверждениях](self-asserted-technical-profile.md#display-claims) о [самостоятельно утвержденном техническом профиле](self-asserted-technical-profile.md).

Пример:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

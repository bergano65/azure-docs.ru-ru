---
title: Определение технического профиля проверки в пользовательской политике в Azure Active Directory B2C | Документы Майкрософт
description: Определение технического профиля Azure Active Directory в пользовательской политике Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511909"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение технического профиля проверки в пользовательской политике в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Технический профиль проверки — это обычный технический профиль любого протокола, такого как [Azure Active Directory](active-directory-technical-profile.md) или [REST API](restful-technical-profile.md). Технический профиль проверки возвращает исходящие утверждения или ошибку HTTP 409 (конфликт ответа) со следующими данными:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Утверждения, возвращаемые из технического профиля проверки, добавляются обратно в контейнер утверждений. Эти утверждения можно использовать в следующих технических профилях проверки.

Технические профили проверки выполняются в той последовательности, в которой они расположены в элементе **ValidationTechnicalProfiles**. В техническом профиле проверки можно настроить, продолжать ли выполнение следующего технического профиля проверки, если технический профиль проверки вызвал ошибку или выполнен успешно.

Можно настроить условное выполнение технического профиля проверки на основе предварительных условий, определенных в элементе **ValidationTechnicalProfile**. Например, вы можете настроить выполнение только в том случае, если существуют определенные утверждения или утверждение равно или не равно заданному значению.

Самоподтвержденный технический профиль может определить технический профиль проверки, используемый для проверки некоторых или всех исходящих утверждений. Все входящие утверждения указанного технического профиля должны присутствовать в списке выходных утверждений технического профиля проверки, в который включен этот элемент.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Элемент **ValidationTechnicalProfiles** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Технический профиль используется для проверки некоторых или всех исходящих утверждений эталонного технического профиля. |

Элемент **ValidationTechnicalProfile** содержит следующий атрибут:

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| ReferenceId | Да | Идентификатор технического профиля, который уже определен в политике или родительской политике. |
|ContinueOnError|Нет| Указывающее, является ли проверка все технические профили последующую проверку следует продолжить, если этот технический профиль проверки вызывает ошибку. Допустимые значения: `true` или `false` (значение по умолчанию, дальнейшая обработка профилей проверки будет остановлена, и будет возвращено сообщение об ошибке). |
|ContinueOnSuccess | Нет | Указывает, следует ли продолжать проверку всех последующих профилей проверки, если этот технический профиль проверки успешно завершился. Возможные значения: `true` или `false`. По умолчанию используется значение `true`, это значит, что обработка профилей проверки будет продолжена. |

Элемент **ValidationTechnicalProfile** содержит следующий элемент:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Список предварительных условий, которые необходимо соблюдать для выполнения технического профиля проверки. |

Элемент **Precondition** содержит следующий атрибут.

| Атрибут | Обязательно для заполнения | Описание |
| --------- | -------- | ----------- |
| `Type` | Да | Тип выполняемой проверки или запроса для данного предварительного условия. Можно установить значение `ClaimsExist`, которое указывает, что действия должны выполняться, если указанные утверждения существуют в текущем наборе утверждений пользователя, или значение `ClaimEquals`, указывающее на то, что действия следует выполнить, если указанное утверждение существует и его значение соответствует указанному. |
| `ExecuteActionsIf` | Да | Указывает, следует ли выполнять действия в предварительном условии, если тест возвращает true или false. |

Элементы **Precondition** содержат следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Value | 1:n | Данные, используемые при проверке. Если тип этой проверки равен `ClaimsExist`, то это поле задает ClaimTypeReferenceId для запроса. Если тип проверки равен `ClaimEquals`, то это поле задает ClaimTypeReferenceId для запроса. А другой элемент значения содержит значение, для которого необходимо выполнить проверку.|
| Действие | 1:1 | Действие, которое требуется выполнить, если в результате проверки предварительного условия в рамках шага оркестрации получено значение true. Значение **Action** установлено в `SkipThisValidationTechnicalProfile`. Указывает, что не следует выполнять связанный технический профиль проверки. |

### <a name="example"></a>Пример

Следующий пример использует эти технические профили проверки:

1. Первый технический профиль проверки проверяет учетные данные пользователя и не продолжает, если возникает ошибка, например недопустимое имя пользователя или неверный пароль.
2. Следующий технический профиль проверки не выполняется, если утверждение userType не существует или значение userType равно `Partner`. Технический профиль проверки пытается считать профиль пользователя из внутренней базы данных клиентов и продолжает работу при возникновении ошибки, например при недоступности службы REST API или при любой внутренней ошибке.
3. Последний технический профиль проверки не выполняется, если утверждение userType не существует или значение userType равно `Customer`. Технический профиль проверки пытается считать профиль пользователя из внутренней базы данных партнеров и продолжает работу при возникновении ошибки, например при недоступности службы REST API или при любой внутренней ошибке.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```

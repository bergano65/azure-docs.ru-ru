---
title: Примеры преобразования утверждений даты для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документы Майкрософт
description: Примеры преобразования утверждений даты для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ff96b9a63e7340788ef2474ce9934145c184e1e1
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542775"
---
# <a name="date-claims-transformations"></a>Преобразования утверждений даты

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований утверждений даты схемы инфраструктуры процедур идентификации в Azure Active Directory (Azure AD) B2C. Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Проверяет, что одно утверждение даты и времени (строковый тип данных) больше, чем второе утверждение даты и времени (строковый тип данных), и создает исключение.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | строка | Тип первого утверждения, который должен быть больше второго утверждения. |
| inputClaim | rightOperand | строка | Тип второго утверждения, который должен быть меньше первого утверждения. |
| InputParameter | AssertIfEqualTo | Логическое | Указывает, выполняется ли это утверждение, если левый операнд равен правому. |
| InputParameter | AssertIfRightOperandIsNotPresent | Логическое | Указывает, выполняется ли это утверждение, если правый операнд отсутствует. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Указывает количество миллисекунд между двумя датами и временем, после которого они считаются равными (например, чтобы учесть разницу в показаниях часов). |

Преобразование утверждений **AssertDateTimeIsGreaterThan** всегда выполняется из [технического профиля проверки](validation-technical-profile.md), вызываемого с помощью [самоподтвержденного технического профиля](self-asserted-technical-profile.md). В метаданных самоподтвержденного технического профиля **DateTimeGreaterThan** задаются сообщения об ошибках, которые технический профиль отображает пользователю.

![Выполнение AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

В следующем примере сравнивается утверждение `currentDateTime` с утверждением `approvedDateTime`. Ошибка возникает в случае, когда `currentDateTime` больше, чем `approvedDateTime`. Преобразование считает значения равными, если они отличаются не более, чем на 5 минут (30 000 миллисекунд).

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Технический профиль проверки `login-NonInteractive` вызывает преобразование утверждений `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Самоподтвержденный технический профиль вызывает технический профиль проверки **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Результат: возникла ошибка


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Преобразует ClaimType **Date** в тип **DateTime**. Преобразование утверждений преобразует формат времени и добавляет 00:00:00 к дате.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | дата | ClaimType, который необходимо преобразовать. |
| OutputClaim | outputClaim | dateTime | ClaimType, который создается после вызова ClaimsTransformation. |

В следующем примере показано преобразование утверждения `dateOfBirth` (тип данных date) в другое утверждение `dateOfBirthWithTime` (тип данных dateTime).

```XML
<ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: 2019-06-01
- Исходящие утверждения:
    - **outputClaim**: 1559347200 (1 Июня 2019 г., 00:00:00)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Получает текущую дату и время в формате UTC и добавляет это значение к ClaimType.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | ClaimType, который создается после вызова ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

* Исходящие утверждения:
    * **currentDateTime**: 1534418820 (16 августа 2018 г., 11:27:00)

## <a name="datetimecomparison"></a>DateTimeComparison

Определяет, является ли одно утверждение dateTime больше, меньше или оно равно другому. Результатом является новый логический параметр ClaimType со значением true или false.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | Первое утверждение dateTime для сравнения. При значении NULL возникает исключение. |
| InputClaim | secondDateTime | dateTime | Второе утверждение dateTime для сравнения. Значение NULL соответствует текущим дате и времени. |
| InputParameter | operator | строка | Одно из следующих значений: "same", "later than" или "earlier than" ("равно", "позже" или "раньше"). |
| InputParameter | timeSpanInSeconds | int | Добавление временного диапазона к первым дате и времени. |
| OutputClaim | result | Логическое | ClaimType, который создается после вызова ClaimsTransformation. |

Используйте это преобразование, чтобы определить, являются ли два утверждения ClaimTypes равными, больше или меньше друг друга. Например, вы можете сохранить время, когда пользователь последний раз принимал ваши условия предоставления услуг (TOS). Через 3 месяца вы можете попросить пользователя принять TOS еще раз.
Чтобы выполнить преобразование утверждений, необходимо сначала получить текущее значение даты и времени, а также время, когда пользователь последний раз принимал TOS.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="greater than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Входные параметры:
    - **operator**: greater than
    - **timeSpanInSeconds**: 7776000 (90 дней)
- Исходящие утверждения: 
    - **result**: true


---
title: Примеры преобразования логических утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования логических утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8afe530a2194fd8c2137f6d4aa7ac97fa8ed9a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695104"
---
# <a name="boolean-claims-transformations"></a>Преобразования логических утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований логических утверждений схемы инфраструктуры процедур идентификации в Azure Active Directory (Azure AD) B2C. Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="andclaims"></a>Утверждение AndClaims

Выполняет операцию AND для двух логических элементов InputClaim и задает элемент OutputClaim с результатом операции.

| Элемент  | TransformationClaimType  | Тип данных  | Примечания |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | Логическое | Первый оцениваемый элемент ClaimType. |
| InputClaim | inputClaim2  | Логическое | Второй оцениваемый элемент ClaimType. |
|outputClaim | outputClaim | Логическое | Элементы ClaimType, создаваемые после вызова этого преобразования утверждений (true или false). |

В следующем преобразовании утверждений показано, как выполнять операцию AND для двух логических элементов ClaimType: `isEmailNotExist` и `isSocialAccount`. Для исходящего утверждения `presentEmailSelfAsserted` присваивается значение `true`, если значения обоих входящих утверждений — `true`. На шаге оркестрации можно использовать необходимое условие для предварительной настройки страницы самоподтверждения, только если электронная почта учетной записи социальной сети пуста.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim1**: true.
    - **inputClaim2**: false.
- Исходящие утверждения:
    - **outputClaim**: false.


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Проверяет, равны ли логические значения двух утверждений, и создает исключение, если это не так.

| Элемент | TransformationClaimType  | Тип данных  | Примечания |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | Логическое | Элемент ClaimType, который необходимо подтвердить. |
| InputParameter |valueToCompareTo | Логическое | Значение для сравнения (true или false). |

Преобразование утверждений **AssertBooleanClaimIsEqualToValue** всегда выполняется с помощью [технического профиля проверки](validation-technical-profile.md), вызываемого через [самоподтвержденный технический профиль](self-asserted-technical-profile.md). Метаданные самоподтвержденного технического профиля **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** управляют сообщением, поступающим пользователю из технического профиля.

![Выполнение AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

В следующем преобразовании утверждений показано, как проверить значение логического элемента ClaimType на основе значения `true`. Если значение ClaimType `accountEnabled` — false, выдается сообщение об ошибке.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Технический профиль проверки `login-NonInteractive` вызывает преобразование утверждений `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Самоподтвержденный технический профиль вызывает технический профиль проверки **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: false.
    - **valueToCompareTo**: true.
- Результат: возникла ошибка.

## <a name="notclaims"></a>NotClaims

Выполняет операцию Not для логического элемента inputClaim и задает элемент outputClaim с результатом операции.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Логическое | Обрабатываемое утверждение. |
| outputClaim | outputClaim | Логическое | Элементы ClaimType, создаваемые после вызова данного ClaimsTransformation (true или false). |

Это преобразование утверждений используется для выполнения логического отрицания утверждения.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: false.
- Исходящие утверждения:
    - **outputClaim**: true.

## <a name="orclaims"></a>OrClaims 

Вычисляет значение Or для двух логических элементов InputClaim и задает элемент outputClaim с результатом операции.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Логическое | Первый оцениваемый элемент ClaimType. |
| InputClaim | inputClaim2 | Логическое | Второй оцениваемый элемент ClaimType. |
| outputClaim | outputClaim | Логическое | Элементы ClaimType, создаваемые после вызова ClaimsTransformation (true или false). |

В следующем преобразовании утверждений показано, как выполнять операцию `Or` для двух логических элементов ClaimType. На шаге оркестрации можно использовать необходимое условие для предварительной настройки страницы самоподтверждения, если значение одного из утверждений — `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim1**: true.
    - **inputClaim2**: false.
- Исходящие утверждения:
    - **outputClaim**: true.


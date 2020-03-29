---
title: Общие примеры преобразования претензий для пользовательских политик
titleSuffix: Azure AD B2C
description: Общие примеры преобразования для схемы интерфейса identity Experience (IEF) Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188551"
---
# <a name="general-claims-transformations"></a>Преобразования общих утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приводятся примеры использования общих преобразований требований схемы интерфейса Identity Experience в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Копирование стоимости претензии к другому. Оба требования должны быть от одного и того же типа.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка, Int | Тип претензии, который должен быть скопирован. |
| outputClaim | outputClaim | строка, Int | ClaimType, который создается после вызова ClaimsTransformation. |

Используйте эту трансформацию претензий для копирования значения из строки или числового утверждения к другому утверждению. Следующий пример копирует значение требования внешнего email для утверждения электронной почты.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **вводаПретензия:**bob@contoso.com
- Исходящие утверждения:
    - **выходная претензия**:bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Проверяет, существует ли **inputClaim**, и устанавливает для элемента **outputClaim** значение true или false соответственно.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Любой | Входящее утверждение, существование которого необходимо проверить. |
| outputClaim | outputClaim | Логическое | ClaimType, который создается после вызова ClaimsTransformation. |

Используйте это преобразование, чтобы проверить, существует ли утверждение или содержит ли оно какое-либо значение. Возвращается логическое значение, указывающее, существует ли утверждение. В следующем примере проверяется, существует ли адрес электронной почты.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **вводаПретензия:**someone@contoso.com
- Исходящие утверждения:
  - **outputClaim**: true.

## <a name="hash"></a>Хэш

Хэширует предоставленный обычный текст, используя случайное значение и секрет. Используемый алгоритм хэширования - SHA-256.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | строка | Входящее утверждение для шифрования |
| InputClaim | salt | строка | Параметр salt. Вы можете создать случайное значение, используя преобразование утверждений `CreateRandomString`. |
| InputParameter | randomizerSecret | строка | Указывает на существующий **ключ политики**Azure AD B2C с развязывательовать. Для создания нового ключа политики: В вашем арендаторе Azure AD B2C под **управлением**выберите **рамочную программу опыта идентификации.** Выберите **ключи политики** для просмотра ключей, доступных в вашем арендаторе. Нажмите кнопку **Добавить**. В пункте **Параметры** выберите **Manual** (Вручную). Укажите имя (префикс *B2C_1A_* может быть добавлен автоматически.). В **секретном** текстовом окне введите любой секрет, который вы хотите использовать, например 1234567890. Для параметра **Использование ключа** выберите **Подпись**. Выберите **Создать**. |
| outputClaim | hash | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. Утверждение, настроенное в inputClaim `plaintext`. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **простой текст**:MyPass@word1
  - **salt**: 487624568.
  - **randomizerSecret**: B2C_1A_AccountTransformSecret.
- Исходящие утверждения:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=

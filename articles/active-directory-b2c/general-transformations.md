---
title: Общие примеры преобразования утверждений для пользовательских политик
titleSuffix: Azure AD B2C
description: Общие примеры преобразования утверждений для схемы инфраструктура процедур идентификации (инфраструктура процедур идентификации) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948902"
---
# <a name="general-claims-transformations"></a>Преобразования общих утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования общих преобразований утверждений схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Проверяет, существует ли **inputClaim**, и устанавливает для элемента **outputClaim** значение true или false соответственно.

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | InputClaim |Любой | Входящее утверждение, существование которого необходимо проверить. |
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
  - **inputClaim**: someone@contoso.com.
- Исходящие утверждения:
  - **outputClaim**: true.

## <a name="hash"></a>Хэш

Хэширует предоставленный обычный текст, используя случайное значение и секрет. Используется алгоритм хэширования SHA-256.

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | plaintext | string | Входящее утверждение для шифрования |
| inputClaim | salt | string | Параметр salt. Вы можете создать случайное значение, используя преобразование утверждений `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Указывает на существующий **ключ политики**Azure AD B2C. Чтобы создать новый ключ политики, в Azure AD B2C клиенте в разделе **Управление**выберите инфраструктура процедур **идентификации**. Выберите **ключи политики** , чтобы просмотреть ключи, доступные в вашем клиенте. Выберите **Добавить**. В пункте **Параметры** выберите **Manual** (Вручную). Укажите имя (префикс *B2C_1A_* может быть добавлен автоматически.). В текстовом поле **секрет** введите любой секрет, который вы хотите использовать, например 1234567890. Для параметра **Использование ключа** выберите **Подпись**. Нажмите кнопку **Создать**. |
| outputClaim | hash | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений. Утверждение, настроенное в inputClaim `plaintext`. |

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
  - **plaintext**: MyPass@word1.
  - **salt**: 487624568.
  - **randomizerSecret**: B2C_1A_AccountTransformSecret.
- Исходящие утверждения:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=

---
title: Примеры преобразования общих утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования общих утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8ff418c24e9171d452bca873c4b8f66ada2adb7c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431332"
---
# <a name="general-claims-transformations"></a>Преобразования общих утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований общих утверждений инфраструктуры процедур идентификации в Azure Active Directory (Azure AD) B2C. Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Проверяет, существует ли **inputClaim**, и устанавливает для элемента **outputClaim** значение true или false соответственно.

| Элемент | TransformationClaimType | Тип данных | Примечания |
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
    - **inputClaim**: someone@contoso.com.
- Исходящие утверждения: 
    - **outputClaim**: true.

## <a name="hash"></a>Хэш

Хэширует предоставленный обычный текст, используя случайное значение и секрет.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | строка | Входящее утверждение для шифрования |
| InputClaim | salt | строка | Параметр salt. Вы можете создать случайное значение, используя преобразование утверждений `CreateRandomString`. |
| InputParameter | randomizerSecret | строка | Указывает на существующие **ключи политики** Azure AD B2C. Чтобы создать новый, в клиенте Azure AD B2C выберите **B2C Settings (Параметры B2C) > Identity Experience Framework**. Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи. Выберите **Добавить**. В пункте **Параметры** выберите **Manual** (Вручную). Предоставьте имя (префикс B2C_1A_ может быть добавлен автоматически). В поле "Секрет" введите любой секрет, который нужно использовать, например 1234567890. Для параметра "Использование ключа" выберите **Секрет**. Нажмите кнопку **Создать**. |
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
    - **plaintext**: MyPass@word1.
    - **salt**: 487624568.
    - **randomizerSecret**: B2C_1A_AccountTransformSecret.
- Исходящие утверждения: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=




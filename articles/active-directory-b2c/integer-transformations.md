---
title: Примеры преобразования целочисленных утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования целочисленных утверждений для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44383034"
---
# <a name="integer-claims-transformations"></a>Преобразования целочисленных утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований целочисленных утверждений для схемы инфраструктуры процедур идентификации в Azure Active Directory (Azure AD) B2C. Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Преобразует тип данных "длинное целое" в тип данных "строка".

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | длинное целое число | ClaimType для преобразования в строку. |
| OutputClaim | outputClaim | строка | ClaimType, который создается после вызова ClaimsTransformation. |

В этом примере утверждение `numericUserId` с типом значения "длинное целое" преобразуется в утверждение `UserId` с типом значения "строка".

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: 12334 (длинное целое)
- Исходящие утверждения: 
    - **outputClaim**: "12334" (строка)


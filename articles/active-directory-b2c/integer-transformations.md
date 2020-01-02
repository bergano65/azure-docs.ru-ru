---
title: Примеры преобразования целочисленных требований для пользовательских политик
titleSuffix: Azure AD B2C
description: Примеры преобразования целочисленных утверждений для схемы инфраструктура процедур идентификации (инфраструктура процедур идентификации) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7295e03f0a0f94b3450b99acc4d10d6ff86c92e7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948908"
---
# <a name="integer-claims-transformations"></a>Преобразования целочисленных утверждений

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приводятся примеры использования преобразования целочисленных утверждений схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Преобразует тип данных "длинное целое" в тип данных "строка".

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | InputClaim | длинное целое число | ClaimType для преобразования в строку. |
| outputClaim | outputClaim | string | ClaimType, который создается после вызова ClaimsTransformation. |

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


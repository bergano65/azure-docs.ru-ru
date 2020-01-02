---
title: Примеры преобразования утверждений StringCollection для пользовательских политик
titleSuffix: Azure AD B2C
description: Примеры преобразования утверждений StringCollection для схемы платформы идентификации (инфраструктура процедур идентификации) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbbd7b4bdddf2b58e66cb1203414b5a63eec2f27
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951009"
---
# <a name="stringcollection-claims-transformations"></a>Преобразования утверждений StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований утверждений коллекций строк схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Добавляет строковое утверждение в новое утверждение stringCollection.

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | item | string | Тип ClaimType, добавляемый к исходящему утверждению. |
| inputClaim | collection | StringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| outputClaim | collection | StringCollection | Типы ClaimType, создаваемые после вызова этого преобразования ClaimsTransformation. |

Используйте это преобразование утверждений, чтобы добавить строку в новый или существующий элемент stringCollection. Оно обычно используется в техническом профиле **AAD-UserWriteUsingAlternativeSecurityId**. Перед созданием новой учетной записи социальной сети преобразование утверждений **CreateOtherMailsFromEmail** считывает ClaimType и добавляет значение в элемент ClaimType **otherMails**.

Следующее преобразование утверждений добавляет элемент ClaimType **email** в элемент ClaimType **otherMails**.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **collection**: ["someone@outlook.com"].
  - **item**: "admin@contoso.com".
- Исходящие утверждения:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"].

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Добавляет строковый параметр в новое утверждение stringCollection.

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | collection | StringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| InputParameter | item | string | Значение, добавляемое к исходящему утверждению. |
| outputClaim | collection | StringCollection | Параметр ClaimTypes, который будет создан после вызова этого ClaimsTransformation. |

Используйте это преобразование утверждений, чтобы добавить строковое значение в новый или существующий элемент stringCollection. В следующем примере постоянный адрес электронной почты (admin@contoso.com) добавляется в утверждение **otherMails**.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **collection**: ["someone@outlook.com"].
- Входные параметры
  - **item**: "admin@contoso.com".
- Исходящие утверждения:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"].

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Получает первый элемент из предоставленной коллекции строк.

| Элемент | TransformationClaimType | Тип данных | Заметки |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | collection | StringCollection | Параметр ClaimTypes, используемый в преобразовании утверждений для получения элемента. |
| outputClaim | extractedItem | string | Типы ClaimType, создаваемые после вызова этого преобразования ClaimsTransformation. Первый элемент в коллекции. |

В следующем примере считывается утверждение **otherMails** и возвращается первый элемент в утверждение **email**.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"].
- Исходящие утверждения:
  - **extractedItem**: "someone@outlook.com".


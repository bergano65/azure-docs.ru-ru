---
title: Примеры преобразования утверждений StringCollection для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования утверждений StringCollection для схемы инфраструктуры процедур идентификации Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 58773dded162ea51ccc6e502bbbdd4e13965c1d6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203494"
---
# <a name="stringcollection-claims-transformations"></a>Преобразования утверждений StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований утверждений коллекции строк схемы инфраструктуры процедур идентификации в Azure Active Directory (Azure AD) B2C. Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Добавляет строковое утверждение в новое утверждение stringCollection. 

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | строка | Тип ClaimType, добавляемый к исходящему утверждению. |
| InputClaim | collection | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| outputClaim | collection | StringCollection | Типы утверждений (ClaimType), создаваемые после вызова данного преобразования утверждений (ClaimsTransformation). |

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

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| InputParameter | item | строка | Значение, добавляемое к исходящему утверждению. |
| outputClaim | collection | stringCollection | Параметр ClaimTypes, который будет создан после вызова этого ClaimsTransformation. |

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

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Параметр ClaimTypes, используемый в преобразовании утверждений для получения элемента. |
| outputClaim | extractedItem | строка | Типы ClaimType, создаваемые после вызова этого преобразования ClaimsTransformation. Первый элемент в коллекции. |

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


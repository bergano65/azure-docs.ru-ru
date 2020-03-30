---
title: StringCollection утверждает примеры трансформации для пользовательских политик
titleSuffix: Azure AD B2C
description: StringCollection претендует на примеры преобразования для схемы интерфейса identity Experience (IEF) Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6aea537ebff4ae61e00861e6cafe742a7feb165e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186783"
---
# <a name="stringcollection-claims-transformations"></a>Преобразования утверждений StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приводятся примеры использования преобразований требований строк и схемы интерфейса Identity Experience в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Добавляет строку претензии к новым уникальным значениям stringsCollection претензии.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | строка | Тип ClaimType, добавляемый к исходящему утверждению. |
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| outputClaim | коллекция | stringCollection | ClaimType, который производится после этого преобразования претензий был вызван, со значением, указанным в претензии ввода. |

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
  - **коллекции**:someone@outlook.com» »
  - **пункт**:admin@contoso.com"
- Исходящие утверждения:
  - **коллекции**:someone@outlook.com"admin@contoso.com" " "

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Добавляет параметр строки к новой уникальной заявке строки значенийCollection.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| InputParameter | item | строка | Значение, добавляемое к исходящему утверждению. |
| outputClaim | коллекция | stringCollection | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением, указанным во входном параметре. |

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
  - **коллекции**:someone@outlook.com» »
- Входные параметры
  - **пункт**:admin@contoso.com"
- Исходящие утверждения:
  - **коллекции**:someone@outlook.com"admin@contoso.com" " "

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Получает первый элемент из предоставленной коллекции строк.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | коллекция | stringCollection | Параметр ClaimTypes, используемый в преобразовании утверждений для получения элемента. |
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
  - **коллекции**:someone@outlook.com"someone@contoso.com" " "
- Исходящие утверждения:
  - **извлеченныйПункт**:someone@outlook.com" "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Проверяет, содержит ли тип требования StringCollection элемент

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Тип претензии, который необходимо искать. |
|InputParameter|item|строка|Значение для поиска.|
|InputParameter|ignoreCase|строка|Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк.|
| outputClaim | outputClaim | Логическое | ClaimType, который создается после вызова ClaimsTransformation. Индикатор булеана, если коллекция содержит такую строку |

Следующий пример `roles` проверяет, содержит ли тип требования stringCollection значение **админ.**

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Входящие утверждения:
    - **inputClaim**: «читатель», «автор», «админ»
- Входные параметры:
    - **пункт**: "Админ"
    - **ignoreCase**: "правда"
- Исходящие утверждения:
    - **выходНая претензия**: "правда"



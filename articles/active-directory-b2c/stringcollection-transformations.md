---
title: Примеры преобразования утверждений StringCollection для пользовательских политик
titleSuffix: Azure AD B2C
description: Примеры преобразования утверждений StringCollection для схемы платформы идентификации (инфраструктура процедур идентификации) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ad9cc3d6d07c8d744ec667e2fffb035848121b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203254"
---
# <a name="stringcollection-claims-transformations"></a>Преобразования утверждений StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований утверждений коллекций строк схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Добавляет утверждение строки в новое уникальное значение утверждения stringCollection.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | строка | Тип ClaimType, добавляемый к исходящему утверждению. |
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| outputClaim | коллекция | stringCollection | Значение параметра, созданное после вызова этого преобразования утверждений, со значением, указанным во входном утверждении. |

Используйте это преобразование утверждений, чтобы добавить строку в новый или существующий элемент stringCollection. Оно обычно используется в техническом профиле **AAD-UserWriteUsingAlternativeSecurityId**. Перед созданием новой учетной записи социальной сети преобразование утверждений **CreateOtherMailsFromEmail** считывает ClaimType и добавляет значение в элемент ClaimType **otherMails**.

Следующее преобразование утверждений добавляет элемент ClaimType **email** в элемент ClaimType **otherMails**.

```xml
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
  - **коллекция**: [" someone@outlook.com "]
  - **элемент**: " admin@contoso.com "
- Исходящие утверждения:
  - **коллекция**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Добавляет строковый параметр в новое уникальное значение утверждения stringCollection.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| InputParameter | item | строка | Значение, добавляемое к исходящему утверждению. |
| outputClaim | коллекция | stringCollection | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением, указанным во входном параметре. |

Используйте это преобразование утверждений, чтобы добавить строковое значение в новый или существующий элемент stringCollection. В следующем примере постоянный адрес электронной почты (admin@contoso.com) добавляется в утверждение **otherMails**.

```xml
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
  - **коллекция**: [" someone@outlook.com "]
- Входные параметры
  - **элемент**: " admin@contoso.com "
- Исходящие утверждения:
  - **коллекция**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Получает первый элемент из предоставленной коллекции строк.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | коллекция | stringCollection | Параметр ClaimTypes, используемый в преобразовании утверждений для получения элемента. |
| outputClaim | extractedItem | строка | Типы ClaimType, создаваемые после вызова этого преобразования ClaimsTransformation. Первый элемент в коллекции. |

В следующем примере считывается утверждение **otherMails** и возвращается первый элемент в утверждение **email**.

```xml
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
  - **коллекция**: [" someone@outlook.com ", " someone@contoso.com "]
- Исходящие утверждения:
  - **екстрактедитем**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>стрингколлектионконтаинс

Проверяет, содержит ли тип утверждения StringCollection элемент

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Тип утверждения, для которого выполняется поиск. |
|InputParameter|item|строка|Значение, которое нужно найти.|
|InputParameter|ignoreCase|строка|Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк.|
| outputClaim | outputClaim | Логическое | ClaimType, который создается после вызова ClaimsTransformation. Логический индикатор, если коллекция содержит такую строку |

В следующем примере проверяется, `roles` содержит ли тип утверждения stringCollection значение **Admin**.

```xml
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
    - **inputClaim**: ["читатель", "author", "admin"]
- Входные параметры:
    - **элемент**: "admin"
    - **ignoreCase**: "true"
- Исходящие утверждения:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>стрингколлектионконтаинсклаим

Проверяет, содержит ли тип утверждения StringCollection значение утверждения.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | коллекция | stringCollection | Тип утверждения, для которого выполняется поиск. |
| InputClaim | item|строка| Тип утверждения, содержащий искомое значение.|
|InputParameter|ignoreCase|строка|Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк.|
| outputClaim | outputClaim | Логическое | ClaimType, который создается после вызова ClaimsTransformation. Логический индикатор, если коллекция содержит такую строку |

В следующем примере проверяется, `roles` содержит ли тип утверждения stringCollection значение `role` типа утверждения.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Входящие утверждения:
    - **коллекция**: ["читатель", "author", "admin"]
    - **элемент**: "admin"
- Входные параметры:
    - **ignoreCase**: "true"
- Исходящие утверждения:
    - **outputClaim**: "true"

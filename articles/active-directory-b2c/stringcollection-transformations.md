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
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982302"
---
# <a name="stringcollection-claims-transformations"></a>Преобразования утверждений StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований утверждений коллекций строк схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Добавляет строковое утверждение в новое утверждение stringCollection.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Тип ClaimType, добавляемый к исходящему утверждению. |
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| outputClaim | коллекция | stringCollection | Типы ClaimType, создаваемые после вызова этого преобразования ClaimsTransformation. |

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
| InputClaim | коллекция | stringCollection | [Необязательно.] Если указано, преобразование утверждений копирует элементы из данной коллекции и добавляет элемент в конец исходящего утверждения коллекции. |
| InputParameter | item | string | Значение, добавляемое к исходящему утверждению. |
| outputClaim | коллекция | stringCollection | Параметр ClaimTypes, который будет создан после вызова этого ClaimsTransformation. |

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
| InputClaim | коллекция | stringCollection | Параметр ClaimTypes, используемый в преобразовании утверждений для получения элемента. |
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


## <a name="stringcollectioncontains"></a>стрингколлектионконтаинс

Проверяет, содержит ли тип утверждения StringCollection элемент

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Тип утверждения, для которого выполняется поиск. |
|InputParameter|item|string|Искомое значение.|
|InputParameter|ignoreCase|string|Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк.|
| outputClaim | outputClaim | Логическое | ClaimType, который создается после вызова ClaimsTransformation. Логический индикатор, если коллекция содержит такую строку |

В следующем примере проверяется, содержит ли тип утверждения `roles` stringCollection значение **Admin**.

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
    - **inputClaim**: ["читатель", "author", "admin"]
- Входные параметры:
    - **элемент**: "admin"
    - **ignoreCase**: "true"
- Исходящие утверждения:
    - **outputClaim**: "true"



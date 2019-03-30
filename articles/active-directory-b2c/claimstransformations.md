---
title: Элемент ClaimsTransformations в Azure Active Directory B2C | Документация Майкрософт
description: Определение элемента ClaimsTransformations для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bc6cc7b07d3dce43a666b3e5b0a958b41cdd3131
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651514"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элемент **ClaimsTransformations** содержит список функций преобразования утверждений, которые могут использоваться в пути взаимодействия пользователя в качестве части [настраиваемой политики](active-directory-b2c-overview-custom.md). Преобразование утверждений преобразует одно утверждение в другое. При преобразовании утверждений нужно указать метод преобразования, например добавление элемента в коллекцию строк или изменение регистра строки.

Чтобы включить список функций преобразования утверждений, который можно использовать в путях взаимодействия пользователя, необходимо объявить XML-элемент ClaimsTransformations в разделе BuildingBlocks политики.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** элемент содержит следующие атрибуты:

| Атрибут |Обязательно для заполнения | ОПИСАНИЕ |
| --------- |-------- | ----------- |
| Идентификатор |Yes | Идентификатор, который уникально определяет преобразование утверждения. На идентификатор ссылаются другие XML-элементы в политике. |
| TransformationMethod | Yes | Метод преобразования, используемый в преобразовании утверждений. Каждое преобразование утверждения имеет собственные значения. Полный список доступных значений см. в разделе [Справочник по преобразованиям утверждений](#claims-transformations-reference). |

## <a name="claimstransformation"></a>ClaimsTransformation

Элемент **ClaimsTransformation** содержит следующие элементы.

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>                
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Список элементов **InputClaim**, определяющих типы утверждений, которые считаются входными данными для преобразования утверждений. Каждый из этих элементов содержит ссылку на тип ClaimType, уже определенный в разделе ClaimsSchema политики. |
| InputParameters | 0:1 | Список элементов **InputParameter**, которые предоставляются в качестве входных данных для преобразования утверждений.  
| OutputClaims | 0:1 | Список элементов **OutputClaim**, указывающих типы утверждений, которые создаются после вызова ClaimsTransformation. Каждый из этих элементов содержит ссылку на тип ClaimType, который уже определен в разделе ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

Элемент **InputClaims** содержит следующий элемент:

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Ожидаемый тип входящего утверждения. |

#### <a name="inputclaim"></a>InputClaim

Элемент **InputClaim** содержит следующие атрибуты:

| Атрибут |Обязательно для заполнения | ОПИСАНИЕ |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Yes | Ссылка на тип ClaimType, уже определенный в разделе ClaimsSchema политики. |
| TransformationClaimType |Yes | Идентификатор для ссылки на тип утверждения преобразования. Каждое преобразование утверждения имеет собственные значения. Полный список доступных значений см. в разделе [Справочник по преобразованиям утверждений](#claims-transformations-reference). |

### <a name="inputparameters"></a>InputParameters

Элемент **InputParameters** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Ожидаемый входной параметр. |

#### <a name="inputparameter"></a>InputParameter

| Атрибут | Обязательно для заполнения |ОПИСАНИЕ |
| --------- | ----------- |----------- |
| Идентификатор | Yes | Идентификатор, который является ссылкой на параметр метода преобразования утверждений. Каждый метод преобразования утверждения имеет собственные значения. Полный список доступных значений см. в таблице по преобразованию утверждений. |
| DataType | Yes | Тип данных параметра, такой как String, Boolean, Int или DateTime, в соответствии с перечислением DataType в схеме XML настраиваемой политики. Этот тип используется для правильного выполнения арифметических операций. Каждое преобразование утверждения имеет собственные значения. Полный список доступных значений см. в разделе [Справочник по преобразованиям утверждений](#claims-transformations-reference). |
| Значение | Yes | Значение, которое передается в преобразование дословно. Некоторые значения являются произвольными, а некоторые из них выбираются из метода преобразования утверждений. |

### <a name="outputclaims"></a>OutputClaims

Элемент **PersistedClaim** содержит следующие элементы:

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| outputClaim | 0:n | Ожидаемый тип исходящего утверждения. |

#### <a name="outputclaim"></a>OutputClaim 

Элемент **OutputClaim** содержит следующие атрибуты:

| Атрибут |Обязательно для заполнения | ОПИСАНИЕ |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Yes | Ссылка на тип ClaimType, уже определенный в разделе ClaimsSchema политики.
| TransformationClaimType | Yes | Идентификатор для ссылки на тип утверждения преобразования. Каждое преобразование утверждения имеет собственные значения. Полный список доступных значений см. в разделе [Справочник по преобразованиям утверждений](#claims-transformations-reference). |
 
Если входящие и исходящие утверждения одного типа (строка или логическое значение), входящее утверждение можно использовать как исходящее. В этом случае преобразование утверждений изменяет входное утверждение с использованием выходного значения.

## <a name="example"></a>Пример

Например, вы можете сохранить последнюю версию условий предоставлений услуг, которую принял пользователь. При обновлении условий предоставления услуг можно предложить пользователю принять новую версию. В следующем примере преобразование утверждения **HasTOSVersionChanged** сравнивает значение утверждения **TOSVersion** со значением утверждения **LastTOSAcceptedVersion**, а затем возвращает логическое значение утверждения **TOSVersionChanged**.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Справочник по преобразованиям утверждений

Примеры преобразования утверждений см. на следующих страницах справки:

- [Логический](boolean-transformations.md)
- [Дата](date-transformations.md)
- [Целое число](integer-transformations.md)
- [JSON](json-transformations.md)
- [Общие сведения](general-transformations.md)
- [Учетная запись социальной сети](social-transformations.md)
- [Строка](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)


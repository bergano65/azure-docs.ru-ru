---
title: Преобразования утверждений телефонных номеров в пользовательских политиках
titleSuffix: Azure AD B2C
description: Справочник по настраиваемой политике для преобразований заявок на телефонные номера в Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 34a6d15090cd13a775ad3faa694718ec58738471
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620627"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Определение преобразований заявок на телефонный номер в Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье содержатся справочные сведения и примеры использования преобразования заявок на телефонные номера в схеме инфраструктуры среды идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения о преобразованиях утверждений в целом см. в разделе [клаимстрансформатионс](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>конвертфоненумберклаимтостринг

Преобразует `phoneNumber` тип данных в `string`ный тип данных.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  ClaimType для преобразования в строку. |
| outputClaim | фоненумберстринг | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

В этом примере утверждение Целлфоненумбер с типом значения `phoneNumber` преобразуется в утверждение Целлфоне с типом значения `string`.

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **PhoneNumber**: + 11234567890 (PhoneNumber)
- Исходящие утверждения:
  - **фоненумберстринг**: + 11234567890 (строка)


## <a name="convertstringtophonenumberclaim"></a>конвертстрингтофоненумберклаим

Это преобразование утверждения проверяет формат номера телефона. Если он имеет допустимый формат, замените его стандартным форматом, используемым Azure AD B2C. Если предоставленный номер телефона имеет недопустимый формат, возвращается сообщение об ошибке.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | фоненумберстринг | строка |  Строковое утверждение для номера телефона. Номер телефона должен быть в международном формате, заканчиваться ведущим символом "+" и кодом страны. Если предоставлено `country` входящего утверждения, номер телефона указывается в локальном формате (без кода страны). |
| InputClaim | country | строка | Используемых Строковое утверждение для кода страны номера телефона в формате ISO3166 (код страны из двух букв ISO-3166). |
| outputClaim | outputClaim | phoneNumber | Результат преобразования утверждений. |

Преобразование « **конвертстрингтофоненумберклаим** Claims» всегда выполняется из [технического профиля проверки](validation-technical-profile.md) , который вызывается [самостоятельно подтвержденным техническим профилем](self-asserted-technical-profile.md) или [элементом управления отображением](display-controls.md). Метаданные технического профиля с самоподтверждением **усермессажеифклаимстрансформатионинвалидфоненумбер** контролирует сообщение об ошибке, представленное пользователю.

![Схема пути выполнения сообщения об ошибке](./media/phone-authentication/assert-execution.png)

Это преобразование утверждений можно использовать, чтобы убедиться, что указанное строковое утверждение является допустимым номером телефона. В противном случае выдается сообщение об ошибке. В следующем примере проверяется, действительно ли **фонестринг** в действительности является действительным номером телефона, а затем возвращается номер телефона в стандартном формате Azure AD B2C. В противном случае выдается сообщение об ошибке.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Самостоятельно утвержденный технический профиль, который вызывает технический профиль проверки, содержащий это преобразование утверждений, может определять сообщение об ошибке.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Пример 1

- Входящие утверждения:
  - **фоненумберстринг**: 033 456-7890
  - **страна**: DK
- Исходящие утверждения:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>Пример 2

- Входящие утверждения:
  - **фоненумберстринг**: + 1 (123) 456-7890
- Исходящие утверждения: 
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>жетнатионалнумберандкаунтрикодефромфоненумберстринг

При этом извлекается код страны и национальный номер из входного утверждения, а также при необходимости создается исключение, если указанный номер телефона является недопустимым.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | строка | Строковое утверждение номера телефона. Номер телефона должен быть в международном формате, заканчиваться ведущим символом "+" и кодом страны. |
| InputParameter | сровексцептиононфаилуре | Логическое | Используемых Параметр, указывающий, создается ли исключение, если номер телефона является недопустимым. Значение по умолчанию — false. |
| InputParameter | каунтрикодетипе | строка | Используемых Параметр, указывающий тип кода страны в выходном утверждении. Доступные значения — **каллингкоде** (Международный код вызова для страны, например + 1) или **ISO3166** (двухбуквенный код страны ISO-3166). |
| outputClaim | натионалнумбер | строка | Строковое утверждение для номера страны в номере телефона. |
| outputClaim | countryCode | строка | Строковое утверждение для кода страны номера телефона. |


Если преобразование « **жетнатионалнумберандкаунтрикодефромфоненумберстринг** Claims» выполняется из [технического профиля проверки](validation-technical-profile.md) , который вызывается [самостоятельно подтвержденным техническим профилем](self-asserted-technical-profile.md) или [действием элемента управления отображением](display-controls.md#display-control-actions), то в метаданных с самоутвержденным техническим профилем отображается сообщение об ошибке, представленное пользователю.

![Схема пути выполнения сообщения об ошибке](./media/phone-authentication/assert-execution.png)

Это преобразование утверждений можно использовать для разбиения полного номера телефона на код страны и номер национальной. Если указан недопустимый номер телефона, можно выдать сообщение об ошибке.

В следующем примере производится попытка разделить номер телефона на национальные номера и код страны. Если номер телефона действителен, номер телефона будет заменен национальным номером. Если номер телефона является недопустимым, исключение не будет выдано, а номер телефона по-прежнему останется исходным значением.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Самостоятельно утвержденный технический профиль, который вызывает технический профиль проверки, содержащий это преобразование утверждений, может определять сообщение об ошибке.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Пример 1

- Входящие утверждения:
  - **PhoneNumber**: + 49 (123) 456-7890
- Входные параметры:
  - **сровексцептиононфаилуре**: false
  - **каунтрикодетипе**: ISO3166
- Исходящие утверждения:
  - **натионалнумбер**: 1234567890
  - **каунтрикоде**: de

### <a name="example-2"></a>Пример 2

- Входящие утверждения:
  - **PhoneNumber**: + 49 (123) 456-7890
- Входные параметры
  - **сровексцептиононфаилуре**: false
  - **каунтрикодетипе**: каллингкоде
- Исходящие утверждения:
  - **натионалнумбер**: 1234567890
  - **каунтрикоде**: + 49

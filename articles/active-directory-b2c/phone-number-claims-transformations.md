---
title: Номер телефона требует преобразований в пользовательских политиках
titleSuffix: Azure AD B2C
description: Ссылка на пользовательские политики для телефонных номеров требует преобразований в Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183943"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Определение преобразований номеров телефона в Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приводятся ссылки и примеры использования преобразований телефонных номеров в схеме интерфейса Identity Experience В Azure Active Directory B2C (Azure AD B2C). Для получения дополнительной информации о преобразованиях претензий в целом [см.](claimstransformations.md)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberclaimTostring

Преобразует тип `phoneNumber` данных `string` в тип данных.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  ClaimType для преобразования в строку. |
| outputClaim | phoneNumberString | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

В этом примере претензия cellPhoneNumber `phoneNumber` с типом значения преобразуется в `string`претензию cellPhone с типом значения.

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
  - **phoneNumber**: 11234567890 (phoneNumber)
- Исходящие утверждения:
  - **phoneNumberString**: 11234567890 (строка)


## <a name="convertstringtophonenumberclaim"></a>ConvertstringtoPhoneNumberclaim

Эта трансформация претензии подтверждает формат номера телефона. Если он находится в допустимом формате, измените его на стандартный формат, используемый Azure AD B2C. Если предоставленный номер телефона не находится в допустимом формате, сообщение об ошибке возвращается.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | строка |  Строка претензии на номер телефона. Номер телефона должен быть в международном формате, в комплекте с ведущим кодом «К» и кантри. Если ввод `country` претензии предоставляется, номер телефона находится в локальном формате (без кода страны). |
| InputClaim | country | строка | (Необязательно) Строка претензии на код страны номер телефона в формате ISO3166 (двухбуквенный ISO-3166 код страны). |
| outputClaim | outputClaim | phoneNumber | Результат этой трансформации претензий. |

**Преобразование претензий ConvertStringToPhoneNumberClaim** всегда выполняется из [технического профиля проверки,](validation-technical-profile.md) который вызывается [самоутверждаемым техническим профилем](self-asserted-technical-profile.md) или [управлением дисплеем.](display-controls.md) **UserMessageIfClaimsTransformationInvalidPhoneNumber** самоутверждаемый технический профиль метаданных контролирует сообщение об ошибке, которое представлено пользователю.

![Диаграмма пути выполнения сообщения об ошибке](./media/phone-authentication/assert-execution.png)

Вы можете использовать это преобразование претензий, чтобы убедиться, что предоставленная строка претензии является действительным номером телефона. В противном случае выдается сообщение об ошибке. В следующем примере проверяется, что **телефонString** ClaimType действительно является действительным номером телефона, а затем возвращает номер телефона в стандартном формате Azure AD B2C. В противном случае будет брошено сообщение об ошибке.

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

Самоутверждаемый технический профиль, который вызывает технический профиль проверки, содержащий это преобразование претензий, может определить сообщение об ошибке.

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
  - **phoneNumberString**: 033 456-7890
  - **страна**: DK
- Исходящие утверждения:
  - **выходная претензия**: 450334567890

### <a name="example-2"></a>Пример 2

- Входящие утверждения:
  - **phoneNumberString**: No1 (123) 456-7890
- Исходящие утверждения:
  - **выходная претензия**: 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberandCountryCodeFromPhoneNumberstring

Это извлекает код страны и национальный номер из претензии на ввод и по желанию выбрасывает исключение, если предоставленный номер телефона недействителен.

| Item | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | строка | Строка претензии номера телефона. Номер телефона должен быть в международном формате, в комплекте с ведущим кодом «К» и кантри. |
| InputParameter | throwExceptiononfailure | Логическое | (Необязательно) Параметр, указывающий, выбрасывается ли исключение, когда номер телефона недействителен. Значение по умолчанию — false. |
| InputParameter | countryCodeType | строка | (Необязательно) Параметр, указывающий тип кода страны в претензии вывода. Доступные значения: **CallingCode** (международный код вызова для страны, например, No1) или **ISO3166** (двухбуквенные код страны ISO-3166). |
| outputClaim | nationalNumber | строка | Строка претензии на национальный номер телефона. |
| outputClaim | countryCode | строка | Строка претензии для кода страны номера телефона. |


Если **Преобразование претензий GetNationalNumberAndCountryNumberNumberNumberString** выполняется из [технического профиля проверки,](validation-technical-profile.md) который вызывается [самоутверждаемым техническим профилем](self-asserted-technical-profile.md) или [действием управления дисплеем,](display-controls.md#display-control-actions)то самоутверждаемый технический профиль Метаданных **UserMessageIfPhoneNumberParseFailure** управляет сообщением об ошибке, которое представлено пользователю.

![Диаграмма пути выполнения сообщения об ошибке](./media/phone-authentication/assert-execution.png)

Вы можете использовать эту трансформацию претензий, чтобы разделить полный номер телефона на код страны и национальный номер. Если предоставленный номер телефона недействителен, вы можете бросить сообщение об ошибке.

Следующий пример пытается разделить номер телефона на национальный номер и код страны. Если номер телефона действителен, номер телефона будет отменен по национальному номеру. Если номер телефона недействителен, исключение не будет брошено, а номер телефона по-прежнему имеет исходное значение.

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

Самоутверждаемый технический профиль, который вызывает технический профиль проверки, содержащий это преобразование претензий, может определить сообщение об ошибке.

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
  - **phoneNumber**: No49 (123) 456-7890
- Входные параметры:
  - **throwExceptionOnFailure**: ложные
  - **countryCodeType**: ISO3166
- Исходящие утверждения:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Пример 2

- Входящие утверждения:
  - **phoneNumber**: No49 (123) 456-7890
- Входные параметры
  - **throwExceptionOnFailure**: ложные
  - **countryCodeType**: CallingCode
- Исходящие утверждения:
  - **nationalNumber**: 1234567890
  - **CountryCode**: 49 евро

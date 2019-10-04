---
title: Примеры преобразования утверждений строк для схемы инфраструктуры процедур идентификации Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования утверждений строк для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 77f0b196777ae0f2ff0b870eac0a01b11854190b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936814"
---
# <a name="string-claims-transformations"></a>Преобразования утверждений строк

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований строковых утверждений схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Сравнение двух утверждений и создание исключения, если они не равны, в соответствии с указанными для сравнения элементами inputClaim1, inputClaim2 и stringComparison.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Тип первого утверждения, которое необходимо сравнить. |
| InputClaim | inputClaim2 | string | Тип второго утверждения, которое необходимо сравнить. |
| InputParameter | stringComparison | string | сравнение строк, одно из значений: Ordinal, OrdinalIgnoreCase. |

Преобразование строк **AssertStringClaimsAreEqual** всегда выполняется из [технического профиля проверки](validation-technical-profile.md), вызываемого с помощью [самоподтвержденного технического профиля](self-asserted-technical-profile.md). Метаданные самоподтвержденного технического профиля **UserMessageIfClaimsTransformationStringsAreNotEqual** управляют сообщением об ошибке, представляемым пользователю.

![Выполнение AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

С помощью этого преобразования можно убедиться, что два параметра ClaimTypes имеют одинаковые значения. В противном случае выдается сообщение об ошибке. В следующем примере проверяется, равен ли параметр ClaimType **strongAuthenticationEmailAddress** параметру ClaimType **email**. В противном случае выдается сообщение об ошибке.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Технический профиль проверки **login-NonInteractive** вызывает преобразование утверждений **AssertEmailAndStrongAuthenticationEmailAddressAreEqual**.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Самоподтвержденный технический профиль вызывает технический профиль проверки **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **inputClaim1**: someone@contoso.com.
  - **inputClaim2**: someone@outlook.com.
    - Входные параметры:
  - **stringComparison**: ordinalIgnoreCase.
- Результат: возникла ошибка.

## <a name="changecase"></a>ChangeCase

Изменяет предоставленное утверждение в нижний или верхний регистр в зависимости от оператора.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Параметр ClaimType, который необходимо изменить. |
| InputParameter | toCase | string | Одно из следующих значений: `LOWER` или `UPPER`. |
| OutputClaim | OutputClaim | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

С помощью этого преобразования утверждений можно изменить любой строковый тип ClaimType в нижний или верхний регистр.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **email**: SomeOne@contoso.com.
- Входные параметры:
    - **toCase**: LOWER
- Исходящие утверждения:
  - **email**: someone@contoso.com.

## <a name="createstringclaim"></a>CreateStringClaim

Создает строковое утверждение на основе предоставленного входного параметра в политике.

| Элемент | TransformationClaimType | Тип данных | Примечания |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | Строка, которую необходимо задать |
| OutputClaim | createdClaim | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением, указанным во входном параметре. |

Это преобразование утверждения позволяет задать строковое значение параметра ClaimType.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входной параметр:
    - **value**: условия предоставления услуг компании Contoso...
- Исходящие утверждения:
    - **createdClaim**: параметр ClaimType TOS содержит значение "Условия предоставления услуг компании Contoso...".

## <a name="compareclaims"></a>CompareClaims

Определяет, равно ли одно строковое утверждение другому. Результатом является новый логический параметр ClaimType со значением `true` или `false`.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Тип первого утверждения, которое необходимо сравнить. |
| InputClaim | inputClaim2 | string | Тип второго утверждения, которое необходимо сравнить. |
| InputParameter | operator | string | Возможные значения: `EQUAL` или `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк. |
| OutputClaim | OutputClaim | boolean | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

Используйте это преобразование, чтобы проверить, равно ли одно утверждение другому. Например, следующее преобразование утверждений проверяет, равно ли значение утверждения **email** утверждению **Verified.Email**.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **inputClaim1**: someone@contoso.com.
  - **inputClaim2**: someone@outlook.com.
- Входные параметры:
    - **operator**:  NOT EQUAL.
    - **ignoreCase**: true.
- Исходящие утверждения:
    - **outputClaim**: true.

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Определяет, равно ли значение утверждения значению входного параметра.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Тип утверждения, которое необходимо сравнить. |
| InputParameter | operator | string | Возможные значения: `EQUAL` или `NOT EQUAL`. |
| InputParameter | compareTo | string | сравнение строк, одно из значений: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк. |
| OutputClaim | OutputClaim | boolean | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

С помощью этого преобразования можно проверить, равно ли утверждение указанному значению. Например, следующее преобразование утверждений проверяет, равно ли значение утверждения **termsOfUseConsentVersion** значению `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример
- Входящие утверждения:
    - **inputClaim1**: v1.
- Входные параметры:
    - **compareTo**: V1
    - **operator**: EQUAL
    - **ignoreCase**: true.
- Исходящие утверждения:
    - **outputClaim**: true.

## <a name="createrandomstring"></a>CreateRandomString

Создает случайную строку с помощью генератора случайных чисел. Если тип генератора случайных чисел — `integer`, при необходимости можно указать параметр начального значения и максимальное число. Необязательный параметр формата строки позволяет форматировать выходные данные, а необязательный параметр Base64 указывает, соответствуют ли выходные данные randomGeneratorType [идентификатор GUID, целое число] outputClaim (строка) в кодировке Base64.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Указывает случайное значение, которое необходимо создать: `GUID` (глобальный уникальный идентификатор) или `INTEGER` (число). |
| InputParameter | stringFormat | string | [Необязательно.] Формат случайного значения. |
| InputParameter | base64 | boolean | [Необязательно.] Преобразование случайного значения в Base64. Если применяется формат строки, значение после формата строки кодируется в Base64. |
| InputParameter | maximumNumber | int | [Необязательно.] Только для типа randomGeneratorType `INTEGER`. Укажите максимальное число. |
| InputParameter | начальное значение  | int | [Необязательно.] Только для типа randomGeneratorType `INTEGER`. Укажите начальное значение для случайного значения. Примечание. Такое же начальное значение обеспечивает такую же последовательность случайных чисел. |
| OutputClaim | OutputClaim | string | Параметр ClaimType, который будет создан после вызова этого преобразования утверждений. Случайное значение. |

В следующем примере создается глобальный уникальный идентификатор. С помощью этого преобразования утверждений создается случайное имя участника-пользователя.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Пример

- Входные параметры:
    - **randomGeneratorType**: GUID
- Исходящие утверждения:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc.

В следующем примере создается случайное целое число от 0 до 1000. Значение форматируется в формат OTP_{случайное значение}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входные параметры:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}.
    - **Base64**: false.
- Исходящие утверждения:
    - **outputClaim**: OTP_853.


## <a name="formatstringclaim"></a>FormatStringClaim

Форматирование утверждения в указанный формат строки. Это преобразование использует метод C# `String.Format`.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string |Элемент ClaimType, который выступает в качестве параметра {0} формата строки. |
| InputParameter | stringFormat | string | Формат строки, включая параметр {0}. |
| OutputClaim | OutputClaim | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

Используйте это преобразование утверждений для форматирования любой строки с одним параметром {0}. В следующем примере создается **userPrincipalName**. Технические профили всех поставщиков удостоверений в социальных сетях, такие как `Facebook-OAUTH`, вызывают **CreateUserPrincipalName** для создания **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9.
- Входные параметры:
    - **stringFormat**: cpim_{0}@{ИД клиента проверяющей стороны}.
- Исходящие утверждения:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com.

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Форматирование двух утверждений в указанный формат строки. Это преобразование использует метод C# **String.Format**.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string | Элемент ClaimType, который выступает в качестве параметра {0} формата строки. |
| inputClaim | InputClaim | string | Элемент ClaimType, который выступает в качестве параметра {1} формата строки. |
| InputParameter | stringFormat | string | Формат строки, включая параметры {0} и {1}. |
| OutputClaim | OutputClaim | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

Используйте это преобразование утверждений для форматирования любой строки с двумя параметрами: {0} и {1}. В следующем примере создается **displayName** с указанным форматом:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim1**: Ivan
    - **inputClaim2**: Voronkov.
- Входные параметры:
    - **stringFormat**: {0} {1}.
- Исходящие утверждения:
    - **outputClaim**: Ivan Voronkov.

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Выполняет поиск элемента из коллекции **Restriction** утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | Утверждение, содержащее текст, поиск которого будет выполняться в утверждениях **restrictionValueClaim** с коллекцией **Restriction**.  |
| OutputClaim | restrictionValueClaim | string | Утверждение, содержащее коллекцию **Restriction**. После вызова преобразования утверждений значение этого утверждения будет содержать значение выбранного элемента. |

В следующем примере выполняется поиск описания сообщения об ошибке на основе ключа ошибки. Утверждение **ResponseMsg** содержит коллекцию сообщений об ошибках для представления пользователю или отправки проверяющей стороне.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Преобразование утверждений ищет текст элемента и возвращает его значение. Если ограничение локализовано с помощью `<LocalizedCollection>`, преобразование утверждений возвращает локализованное значение.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **mapFromClaim**: B2C_V1_90001.
- Исходящие утверждения:
    - **restrictionValueClaim**: "Вы не можете войти, так как являетесь несовершеннолетним".

## <a name="lookupvalue"></a>LookupValue

Выполняет поиск значения утверждения из списка значений, исходя из значения другого утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | Утверждение, содержащее значение подстановки. |
| InputParameter | |string | Коллекция inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Контролирует, будет ли возвращена ошибка, если значение подстановки не найдено. |
| OutputClaim | inputParameterId | string | Параметр ClaimType, который будет создан после вызова этого преобразования утверждений. Значение соответствующего идентификатора. |

В следующем примере выполняется поиск доменного имени в одной из коллекций inputParameters. Преобразование утверждений ищет доменное имя в идентификаторе и возвращает его значение (идентификатор приложения).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputParameterId**: test.com.
- Входные параметры:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1.
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e.
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9.
    - **errorOnFailedLookup**: false.
- Исходящие утверждения:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9.

## <a name="nullclaim"></a>NullClaim

Удаление значения данного утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | Устанавливает для утверждения значение NULL. |

Это преобразование утверждений используется для удаления ненужных данных из контейнера свойств утверждения. Таким образом файл cookie сеанса будет меньшего размера. В следующем примере удаляется значение типа утверждения `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Входящие утверждения:
    - **outputClaim**: "Добро пожаловать в приложение Contoso". Если вы по-прежнему просматриваете и используете этот веб-сайт, вы соглашаетесь соблюдать следующие положения и условия...
- Исходящие утверждения:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Получает доменную часть адреса электронной почты.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | Параметр ClaimType, содержащий адрес электронной почты. |
| OutputClaim | домен | string | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением домена. |

Это преобразование утверждений используется для анализа доменного имени после символа @ пользователя. Это может быть полезно при удалении персональных данных (PII) из данных аудита. В следующих преобразованиях утверждений показано, как проанализировать доменное имя из утверждения **email**.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **EmailAddress**: joe@outlook.com.
- Исходящие утверждения:
    - **domain**: outlook.com.

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Проверяет, равны ли строка утверждения и входной параметр `matchTo`, и задает для исходящих утверждений значение, указанное во входных параметрах `stringMatchMsg` и `stringMatchMsgCode`, а также задает для исходящего утверждения результата сравнения значение `true` или `false` в зависимости от результата сравнения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Тип утверждения, которое необходимо сравнить. |
| InputParameter | matchTo | string | Строка для сравнения с `inputClaim`. |
| InputParameter | stringComparison | string | Возможные значения: `Ordinal` или `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Первое значение, которое необходимо задать, если строки равны. |
| InputParameter | stringMatchMsgCode | string | Второе значение, которое необходимо задать, если строки равны. |
| OutputClaim | outputClaim1 | string | Если строки равны, это исходящее утверждение содержит значение входного параметра `stringMatchMsg`. |
| OutputClaim | outputClaim2 | string | Если строки равны, это исходящее утверждение содержит значение входного параметра `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | boolean | Тип исходящего утверждения результата сравнения, для которого необходимо задать значение `true` или `false` на основе результата сравнения. |

С помощью этого преобразования можно проверить, равно ли утверждение указанному значению. Например, следующее преобразование утверждений проверяет, равно ли значение утверждения **termsOfUseConsentVersion** значению `v1`. Если да, измените значение на `v2`.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: v1.
- Входные параметры:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase.
    - **stringMatchMsg**:  B2C_V1_90005.
    - **stringMatchMsgCode**:  "TOS обновлено до версии 2".
- Исходящие утверждения:
    - **outputClaim1**: B2C_V1_90005.
    - **outputClaim2**: "TOS обновлено до версии 2".
    - **stringCompareResultClaim**: true.

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Проверяет, равны ли строка утверждения и входной параметр `matchTo`, и задает для исходящих утверждений значение, указанное во входном параметре `outputClaimIfMatched`, а также задает для исходящего утверждения результата сравнения значение `true` или `false` в зависимости от результата сравнения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Тип утверждения, которое необходимо сравнить. |
| InputParameter | matchTo | string | Строка для сравнения с inputClaim. |
| InputParameter | stringComparison | string | Возможные значения: `Ordinal` или `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | Значение, которое необходимо задать, если строки равны. |
| OutputClaim | OutputClaim | string | Если строки равны, это исходящее утверждение содержит значение входного параметра `outputClaimIfMatched`. Или значение NULL, если строки не совпадают. |
| OutputClaim | stringCompareResultClaim | boolean | Тип исходящего утверждения результата сравнения, для которого необходимо задать значение `true` или `false` на основе результата сравнения. |

Например, следующее преобразование утверждений проверяет, равно ли значение утверждения **ageGroup** значению `Minor`. Если да, возвращает значение для `B2C_V1_90001`.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **claimToMatch**: Minor.
- Входные параметры:
    - **matchTo**: Minor.
    - **stringComparison**: ordinalIgnoreCase.
    - **outputClaimIfMatched**:  B2C_V1_90001.
- Исходящие утверждения:
    - **isMinorResponseCode**: B2C_V1_90001.
    - **isMinor**: true.


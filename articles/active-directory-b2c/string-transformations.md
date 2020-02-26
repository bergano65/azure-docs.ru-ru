---
title: Примеры преобразования строковых утверждений для пользовательских политик
titleSuffix: Azure AD B2C
description: Примеры преобразования строковых утверждений для схемы инфраструктура процедур идентификации (инфраструктура процедур идентификации) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 69091fbcc2b6789abc7825632a56197427d34e4c
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045347"
---
# <a name="string-claims-transformations"></a>Преобразования утверждений строк

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье приведены примеры использования преобразований строковых утверждений схемы инфраструктуры процедур идентификации в Azure Active Directory B2C (Azure AD B2C). Дополнительные сведения см. в статье о [преобразовании утверждений](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Сравнение двух утверждений и создание исключения, если они не равны, в соответствии с указанными для сравнения элементами inputClaim1, inputClaim2 и stringComparison.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | строка | Тип первого утверждения, которое необходимо сравнить. |
| InputClaim | inputClaim2 | строка | Тип второго утверждения, которое необходимо сравнить. |
| InputParameter | stringComparison | строка | сравнение строк, одно из значений: Ordinal, OrdinalIgnoreCase. |

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
| InputClaim | inputClaim1 | строка | Изменяемый параметр. |
| InputParameter | toCase | строка | Одно из следующих значений: `LOWER` или `UPPER`. |
| outputClaim | outputClaim | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

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
    - **toCase**: LOWER.
- Исходящие утверждения:
  - **email**: someone@contoso.com.

## <a name="createstringclaim"></a>CreateStringClaim

Создает строковое утверждение на основе предоставленного входного параметра в политике.

| Элемент | TransformationClaimType | Тип данных | Примечания |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | строка | Строка, которую необходимо задать |
| outputClaim | createdClaim | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением, указанным во входном параметре. |

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
| InputClaim | inputClaim1 | строка | Тип первого утверждения, которое необходимо сравнить. |
| InputClaim | inputClaim2 | строка | Тип второго утверждения, которое необходимо сравнить. |
| InputParameter | оператор | строка | Возможные значения: `EQUAL` или `NOT EQUAL`. |
| InputParameter | ignoreCase | Логическое | Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк. |
| outputClaim | outputClaim | Логическое | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

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
    - **operator**: NOT EQUAL.
    - **ignoreCase**: true.
- Исходящие утверждения:
    - **outputClaim**: true.

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Определяет, равно ли значение утверждения значению входного параметра.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | строка | Тип утверждения, которое необходимо сравнить. |
| InputParameter | оператор | строка | Возможные значения: `EQUAL` или `NOT EQUAL`. |
| InputParameter | compareTo | строка | сравнение строк, одно из значений: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | Логическое | Указывает, следует ли в этом сравнении игнорировать регистр сравниваемых строк. |
| outputClaim | outputClaim | Логическое | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

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
    - **compareTo**: V1.
    - **оператор**: EQUAL.
    - **ignoreCase**: true.
- Исходящие утверждения:
    - **outputClaim**: true.

## <a name="createrandomstring"></a>CreateRandomString

Создает случайную строку с помощью генератора случайных чисел. Если тип генератора случайных чисел — `integer`, при необходимости можно указать параметр начального значения и максимальное число. Необязательный параметр формата строки позволяет форматировать выходные данные, а необязательный параметр Base64 указывает, соответствуют ли выходные данные randomGeneratorType [идентификатор GUID, целое число] outputClaim (строка) в кодировке Base64.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | строка | Указывает случайное значение, которое необходимо создать: `GUID` (глобальный уникальный идентификатор) или `INTEGER` (число). |
| InputParameter | stringFormat | строка | [Необязательно.] Формат случайного значения. |
| InputParameter | base64 | Логическое | [Необязательно.] Преобразование случайного значения в Base64. Если применяется формат строки, значение после формата строки кодируется в Base64. |
| InputParameter | maximumNumber | INT | [Необязательно.] Только для типа randomGeneratorType `INTEGER`. Укажите максимальное число. |
| InputParameter | seed  | INT | [Необязательно.] Только для типа randomGeneratorType `INTEGER`. Укажите начальное значение для случайного значения. Примечание. Такое же начальное значение обеспечивает такую же последовательность случайных чисел. |
| outputClaim | outputClaim | строка | Параметр ClaimType, который будет создан после вызова этого преобразования утверждений. Случайное значение. |

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
    - **randomGeneratorType**: глобальный уникальный идентификатор.
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
    - **randomGeneratorType**: INTEGER.
    - **maximumNumber**: 1000.
    - **stringFormat**: OTP_{0}.
    - **Base64**: false.
- Исходящие утверждения:
    - **outputClaim**: OTP_853.


## <a name="formatstringclaim"></a>FormatStringClaim

Форматирование утверждения в указанный формат строки. Это преобразование использует метод C# `String.Format`.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |строка |Элемент ClaimType, который выступает в качестве параметра {0} формата строки. |
| InputParameter | stringFormat | строка | Формат строки, включая параметр {0}. |
| outputClaim | outputClaim | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

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

Форматирование двух утверждений в указанный формат строки. Это преобразование использует метод C# `String.Format`.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |строка | Элемент ClaimType, который выступает в качестве параметра {0} формата строки. |
| InputClaim | InputClaim | строка | Элемент ClaimType, который выступает в качестве параметра {1} формата строки. |
| InputParameter | stringFormat | строка | Формат строки, включая параметры {0} и {1}. |
| outputClaim | outputClaim | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений. |

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
    - **inputClaim1**: Joe.
    - **inputClaim2**: Fernando.
- Входные параметры:
    - **StringFormat**: {0} {1}
- Исходящие утверждения:
    - **outputClaim**: Joe Fernando.

## <a name="getlocalizedstringstransformation"></a>жетлокализедстрингстрансформатион 

Копирует локализованные строки в утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | Имя локализованной строки | строка | Список типов утверждений, созданных после вызова этого преобразования утверждений. |

Чтобы использовать преобразование Жетлокализедстрингстрансформатион Claims, выполните следующие действия.

1. Определите [строку локализации](localization.md) и свяжите ее с [самостоятельно утвержденным-техническим профилем](self-asserted-technical-profile.md).
1. `ElementType` элемента `LocalizedString` должен иметь значение `GetLocalizedStringsTransformationClaimType`.
1. `StringId` — это уникальный идентификатор, который вы определяете и используете позже в преобразовании утверждений.
1. В преобразовании «утверждения» укажите список заявок, которые должны быть заданы в локализованной строке. `ClaimTypeReferenceId` — это ссылка на множество, уже определенное в разделе ClaimsSchema политики. `TransformationClaimType` — это имя локализованной строки, как определено в `StringId` элемента `LocalizedString`.
1. В [самостоятельно подтвержденном техническом профиле](self-asserted-technical-profile.md)или преобразовании "входные или выходные утверждения" [элемента управления](display-controls.md) сделайте ссылку на преобразование утверждений.

![жетлокализедстрингстрансформатион](./media/string-transformations/get-localized-strings-transformation.png)

В следующем примере выполняется поиск темы сообщения электронной почты, текста, сообщения кода и подписи сообщения электронной почты из локализованных строк. Эти утверждения позже используются настраиваемым шаблоном проверки электронной почты.

Определите локализованные строки для английского языка (по умолчанию) и испанского.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Преобразование «утверждения» устанавливает значение *субъекта* типа утверждения со значением `StringId` *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Исходящие утверждения:
  - **Тема**: код проверки электронной почты учетной записи contoso
  - **сообщение**: Спасибо за проверку вашей учетной записи! 
  - **кодеинтро**: ваш код 
  - **подпись**: с уважением  


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Выполняет поиск элемента из коллекции **Restriction** утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | строка | Утверждение, содержащее текст, поиск которого будет выполняться в утверждениях **restrictionValueClaim** с коллекцией **Restriction**.  |
| outputClaim | restrictionValueClaim | строка | Утверждение, содержащее коллекцию **Restriction**. После вызова преобразования утверждений значение этого утверждения будет содержать значение выбранного элемента. |

В следующем примере выполняется поиск описания сообщения об ошибке на основе ключа ошибки. Утверждение **ResponseMsg** содержит коллекцию сообщений об ошибках для представления пользователю или отправки проверяющей стороне.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
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
    - **рестриктионвалуеклаим**: вы не можете войти в систему, так как вы является дополнительным.

## <a name="lookupvalue"></a>LookupValue

Выполняет поиск значения утверждения из списка значений, исходя из значения другого утверждения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | строка | Утверждение, содержащее значение подстановки. |
| InputParameter | |строка | Коллекция inputParameters. |
| InputParameter | errorOnFailedLookup | Логическое | Контролирует, будет ли возвращена ошибка, если значение подстановки не найдено. |
| outputClaim | inputParameterId | строка | Параметр ClaimType, который будет создан после вызова этого преобразования утверждений. Значение сопоставления `Id`. |

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
| outputClaim | claim_to_null | строка | Устанавливает для утверждения значение NULL. |

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
    - **outputClaim**: NULL.

## <a name="parsedomain"></a>ParseDomain

Получает доменную часть адреса электронной почты.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | строка | Параметр ClaimType, содержащий адрес электронной почты. |
| outputClaim | домен | строка | Параметр ClaimType, который создается после вызова этого преобразования утверждений, со значением домена. |

Это преобразование утверждений используется для анализа доменного имени после символа @ пользователя. В следующих преобразованиях утверждений показано, как проанализировать доменное имя из утверждения **email**.

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

## <a name="setclaimsifregexmatch"></a>сетклаимсифрежексматч

Проверяет, равны ли строковые утверждения `claimToMatch` и `matchTo` входной параметр, и устанавливает выходные утверждения со значением, присутствующим в `outputClaimIfMatched` входном параметре, вместе с утверждением результата сравнения результатов, которое устанавливается как `true` или `false` в зависимости от результата сравнения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | строка | Тип утверждения, которое необходимо сравнить. |
| InputParameter | matchTo | строка | Регулярное выражение для сопоставления. |
| InputParameter | outputClaimIfMatched | строка | Значение, которое необходимо задать, если строки равны. |
| outputClaim | outputClaim | строка | Если регулярное выражение совпадает, это выходное утверждение содержит значение `outputClaimIfMatched` входного параметра. Или null, если совпадений нет. |
| outputClaim | режекскомпарересултклаим | Логическое | Тип исходящего утверждения результата сопоставления регулярных выражений, который должен быть установлен в качестве `true` или `false` в зависимости от результата сопоставления. |

Например, проверяет, является ли указанный номер телефона допустимым, основываясь на шаблоне регулярного выражения номера телефона.  

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **клаимтоматч**: "64854114520"
- Входные параметры:
    - **матчто**: "^ [0-9]{4,16}$"
    - **аутпутклаимифматчед**: "Телефон"
- Исходящие утверждения:
    - **outputClaim**: "Телефон"
    - **режекскомпарересултклаим**: true

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Проверяет, равны ли строка утверждения и входной параметр `matchTo`, и задает для исходящих утверждений значение, указанное во входных параметрах `stringMatchMsg` и `stringMatchMsgCode`, а также задает для исходящего утверждения результата сравнения значение `true` или `false` в зависимости от результата сравнения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка | Тип утверждения, которое необходимо сравнить. |
| InputParameter | matchTo | строка | Строка для сравнения с `inputClaim`. |
| InputParameter | stringComparison | строка | Возможные значения: `Ordinal` или `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | строка | Первое значение, которое необходимо задать, если строки равны. |
| InputParameter | stringMatchMsgCode | строка | Второе значение, которое необходимо задать, если строки равны. |
| outputClaim | outputClaim1 | строка | Если строки равны, это исходящее утверждение содержит значение входного параметра `stringMatchMsg`. |
| outputClaim | outputClaim2 | строка | Если строки равны, это исходящее утверждение содержит значение входного параметра `stringMatchMsgCode`. |
| outputClaim | stringCompareResultClaim | Логическое | Тип исходящего утверждения результата сравнения, для которого необходимо задать значение `true` или `false` на основе результата сравнения. |

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
    - **matchTo**: V1.
    - **stringComparison**: ordinalIgnoreCase.
    - **stringMatchMsg**: B2C_V1_90005.
    - **stringMatchMsgCode**: "TOS обновлено до версии 2".
- Исходящие утверждения:
    - **outputClaim1**: B2C_V1_90005.
    - **outputClaim2**: "TOS обновлено до версии 2".
    - **stringCompareResultClaim**: true.

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Проверяет, равны ли строка утверждения и входной параметр `matchTo`, и задает для исходящих утверждений значение, указанное во входном параметре `outputClaimIfMatched`, а также задает для исходящего утверждения результата сравнения значение `true` или `false` в зависимости от результата сравнения.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | строка | Тип утверждения, которое необходимо сравнить. |
| InputParameter | matchTo | строка | Строка для сравнения с inputClaim. |
| InputParameter | stringComparison | строка | Возможные значения: `Ordinal` или `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | строка | Значение, которое необходимо задать, если строки равны. |
| outputClaim | outputClaim | строка | Если строки равны, это исходящее утверждение содержит значение входного параметра `outputClaimIfMatched`. Или значение NULL, если строки не совпадают. |
| outputClaim | stringCompareResultClaim | Логическое | Тип исходящего утверждения результата сравнения, для которого необходимо задать значение `true` или `false` на основе результата сравнения. |

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


## <a name="stringcontains"></a>StringContains;

Определить, находится ли заданная подстрока во входном утверждении. Результатом является новый логический параметр ClaimType со значением `true` или `false`. `true`, если параметр value встречается в этой строке, в противном случае — `false`.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка | Тип утверждения, для которого выполняется поиск. |
|InputParameter|contains|строка|Искомое значение.|
|InputParameter|ignoreCase|строка|Указывает, должно ли это сравнение учитывать регистр сравниваемой строки.|
| outputClaim | outputClaim | строка | ClaimType, который создается после вызова ClaimsTransformation. Логический индикатор, если подстрока встречается во входном утверждении. |

Используйте это преобразование утверждений, чтобы проверить, содержит ли строковый тип утверждения подстроку. В следующем примере проверяется, содержит ли тип `roles` строкового утверждения значение **Admin**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: "Администратор, утверждающий, редактор"
- Входные параметры:
    - **содержит**: "admin".
    - **ignoreCase**: true.
- Исходящие утверждения:
    - **outputClaim**: true. 

## <a name="stringsubstring"></a>стрингсубстринг

Извлекает части типа строкового утверждения, начиная с символа в указанной позиции, и возвращает указанное число символов.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка | Тип утверждения, который содержит строку. |
| InputParameter | startIndex | INT | Отсчитываемый от нуля начальный символ подстроки в данном экземпляре. |
| InputParameter | length | INT | Число символов в подстроке. |
| outputClaim | outputClaim | Логическое | Строка, эквивалентная подстроке length, которая начинается с startIndex в данном экземпляре, или значение Empty, если startIndex равен длине данного экземпляра, а длина равна нулю. |

Например, получите префикс страны для номера телефона.  


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: "+ 1644114520"
- Входные параметры:
    - **startIndex**: 0
    - **Длина**: 2
- Исходящие утверждения:
    - **outputClaim**: "+ 1"

## <a name="stringreplace"></a>стрингреплаце

Выполняет поиск указанного значения в строке типа утверждения и возвращает новую строку типа утверждения, в которой все вхождения указанной строки в текущей строке заменяются другой заданной строкой.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка | Тип утверждения, который содержит строку. |
| InputParameter | старое_значение | строка | Искомая строка. |
| InputParameter | newValue | строка | Строка для замены всех вхождений `oldValue` |
| outputClaim | outputClaim | Логическое | Строка, эквивалентная текущей строке за исключением того, что все экземпляры oldValue заменяются на newValue. Если значение oldValue не найдено в текущем экземпляре, метод возвращает текущий экземпляр без изменений. |

Например, нормализация номера телефона путем удаления `-` символов  


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Пример

- Входящие утверждения:
    - **inputClaim**: "+ 164-411-452-054"
- Входные параметры:
    - **OldValue**: "-"
    - **Длина**: ""
- Исходящие утверждения:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>стрингжоин

Сцепляет элементы указанного типа утверждения коллекции строк, используя заданный разделитель между каждым элементом или членом.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Коллекция, содержащая строки для сцепления. |
| InputParameter | разделитель | строка | Строка, используемая в качестве разделителя, например запятая `,`. |
| outputClaim | outputClaim | строка | Строка, состоящая из элементов коллекции строк `inputClaim`, разделенных входным параметром `delimiter`. |
  
Следующий пример принимает коллекцию строк для ролей пользователей и преобразует ее в строку разделителя в виде запятой. Этот метод можно использовать для хранения коллекции строк в учетной записи пользователя Azure AD. Позже, при чтении учетной записи из каталога, используйте `StringSplit` для преобразования строки разделителя запятой обратно в коллекцию строк.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **inputClaim**: [«Admin», «Author», «Reader»]
- Входные параметры:
  - **Разделитель**: ","
- Исходящие утверждения:
  - **outputClaim**: "Администратор, автор, читатель"


## <a name="stringsplit"></a>стрингсплит

Возвращает массив строк, содержащий подстроки в данном экземпляре, разделенные элементами указанной строки.

| Элемент | TransformationClaimType | Тип данных | Примечания |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | строка | Тип строкового утверждения, который содержит подстрочные строки для разбиения. |
| InputParameter | разделитель | строка | Строка, используемая в качестве разделителя, например запятая `,`. |
| outputClaim | outputClaim | stringCollection | Коллекция строк, элементы которой содержат подстроки в этой строке, разделенные входным параметром `delimiter`. |
  
В следующем примере принимается строка с разделителями-запятыми для ролей пользователей и преобразуется в коллекцию строк.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Пример

- Входящие утверждения:
  - **inputClaim**: "Администратор, автор, читатель"
- Входные параметры:
  - **Разделитель**: ","
- Исходящие утверждения:
  - **outputClaim**: [«Admin», «Author», «Reader»]

---
title: Локализация - Активный каталог Azure B2C
description: Использование элемента Localization в настраиваемой политике для Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681412"
---
# <a name="localization"></a>Локализация

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элемент **Localization** обеспечивает поддержку несколько языковых стандартов или языков в политике для путей взаимодействия пользователя. Поддержка локализации в политиках позволяет выполнить следующее:

- настроить в политике явный список поддерживаемых языков и выбрать язык по умолчанию;
- использовать строки и коллекции, зависящие от языка.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Элемент **Localization** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| Активировано | нет | Возможные значения: `true` или `false`. |

Элемент **Localization** содержит следующие элементы XML:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Список поддерживаемых языков. |
| LocalizedResources | 0:n | Список локализованных ресурсов. |

## <a name="supportedlanguages"></a>SupportedLanguages

Элемент **SupportedLanguages** содержит следующие атрибуты.

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| DefaultLanguage | Да | Язык, который будет использоваться по умолчанию для локализованных ресурсов. |
| MergeBehavior | нет | Перечисление значений, которые объединяются с любым элементом ClaimType с таким же идентификатором в родительской политике. Используйте этот атрибут, чтобы переопределить определенное в базовой политике утверждение. Возможные значения: `Append`, `Prepend` или `ReplaceAll`. Значение `Append` означает, что указанная коллекция данных должна добавляться в конец коллекции, указанной в родительской политике. Значение `Prepend` означает, что указанная коллекция данных должна добавляться в начало коллекции, указанной в родительской политике. Значение `ReplaceAll` означает, что определенную в родительской политике коллекцию данных нужно игнорировать и заменить данными, определенными в текущей политике. |

### <a name="supportedlanguages"></a>SupportedLanguages

Элемент **SupportedLanguages** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Отображает содержимое, которое соответствует тега языка в RFC 5646 (теги для идентификации языков). |

## <a name="localizedresources"></a>LocalizedResources

Элемент **LocalizedResources** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор, который используется для уникальной идентификации локализованных ресурсов. |

Элемент **LocalizedResources** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Определяет целые коллекции для разных языков и региональных параметров. Коллекция может содержать разное количество элементов и разных строк для разных языков и региональных параметров. Примерами коллекций можно считать перечисления, которые указываются в типах утверждений. Например, содержимое раскрывающегося списка стран или регионов, отображаемое для пользователя. |
| LocalizedStrings | 0:n | Определяет все строки, за исключением включенных в коллекции, для разных языков и региональных параметров. |

### <a name="localizedcollections"></a>LocalizedCollections

Элемент **LocalizedCollections** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Список поддерживаемых языков. |

#### <a name="localizedcollection"></a>LocalizedCollection

Элемент **LocalizedCollections** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| ElementType | Да | Ссылается на элемент ClaimType или элемент пользовательского интерфейса в файле политики. |
| ElementId | Да | Строка, содержащая ссылку на тип утверждения, который уже определен в разделе ClaimsSchema и используется, если **ElementType** имеет значение ClaimType. |
| TargetCollection | Да | Целевая коллекция. |

Элемент **LocalizedCollections** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| Элемент | 0:n | Определяет варианты, которые пользователь может выбрать для утверждения в пользовательском интерфейсе, например, значения в раскрывающемся списке. |

Элемент **Item** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| текст | Да | Понятная для пользователя отображаемая строка, которая размещается в пользовательском интерфейсе для этого параметра. |
| Значение | Да | Строковое значение утверждения, связанное с этим вариантом. |
| SelectByDefault | нет | Указывает, следует ли по умолчанию выбирать этот параметр в пользовательском интерфейсе. Возможные значения: True или False. |

Следующий пример демонстрирует использование элемента **LocalizedCollections**. Он содержит два элемента **LocalizedCollection** по одному для английского и испанского языков. Оба они устанавливают для утверждения `Gender` коллекцию **Restriction** со списком элементов на английском и испанском языках.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Элемент **LocalizedStrings** содержит следующие элементы:

| Элемент | Вхождения | Описание |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Локализованная строка. |

Элемент **LocalizedStrings** содержит следующие атрибуты:

| attribute | Обязательно | Описание |
| --------- | -------- | ----------- |
| ElementType | Да | Возможные значения: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Предикат](#predicate), [Входная валидация](#inputvalidation), или [UxElement](#uxelement).   | 
| ElementId | Да | Если **ElementType** настроен `ClaimType` `Predicate`на `InputValidation`, или, этот элемент содержит ссылку на тип претензии, уже определенный в разделе ClaimsSchema. |
| StringId | Да | Если **ElementType** имеет значение `ClaimType`, этот элемент содержит ссылку на атрибут типа утверждения. Возможные значения: `DisplayName`, `AdminHelpText` или `PatternHelpText`. Значение `DisplayName` задает отображаемое имя утверждения. Значение `AdminHelpText` задает имя текста справки для пользователя утверждения. Значение `PatternHelpText` задает текста справки по шаблону утверждения. Если **ElementType** имеет значение `UxElement`, этот элемент содержит ссылку на атрибут элемента пользовательского интерфейса. Если **ElementType** имеет значение `ErrorMessage`, этот элемент задает идентификатор сообщения об ошибке. Полный список идентификаторов `UxElement` для строк локализации можно найти на [этой странице](localization-string-ids.md).|

## <a name="elementtype"></a>ElementType

Ссылка ElementType на тип претензии, преобразование претензии или элемент пользовательского интерфейса в политике, который будет локализован.

| Элемент для локализации | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Имя поставщика идентификационных данных |`ClaimsProvider`| | Идентификатор элемента ClaimsExchange|
| Атрибуты типа претензий|`ClaimType`|Название типа претензии| Атрибут претензии будет локализован. Возможные `AdminHelpText`значения: `DisplayName` `PatternHelpText`, `UserHelpText`, и .|
|Сообщение об ошибке|`ErrorMessage`||Идентификатор сообщения об ошибке |
|Копии локализованных строк в претензии|`GetLocalizedStringsTra nsformationClaimType`||Название претензии на вывод|
|Предиката юношного сообщения|`Predicate`|Название предиката| Атрибут предиката, который будет локализован. Возможные значения: `HelpText`.|
|Предикат групповое сообщение пользователя|`InputValidation`|Идентификатор элемента PredicateValidation.|Идентификатор элемента PredicateGroup. Предикатная группа должна быть ребенком элемента предикатной проверки, как это определено в ElementId.|
|Элементы пользовательского интерфейса |`UxElement` | | Идентификатор элемента пользовательского интерфейса, который будет локализован.|

## <a name="examples"></a>Примеры

### <a name="claimsprovider"></a>ClaimsProvider

Значение ClaimsProvider используется для локализации имени одного из поставщиков заявок. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

В следующем примере показано, как локализовать имя поставщика претензий.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

Значение ClaimType используется для локализации одного из атрибутов претензии. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

В следующем примере показано, как локализовать атрибуты DisplayName, UserHelpText и PatternHelpText типа запроса электронной почты.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

Значение ErrorMessage используется для локализации одного из сообщений системных ошибок. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

В следующем примере показано, как локализовать сообщение об ошибке UserMessageIfClaimsPrincipalAlreadyExists.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Значение GetLocalizedStringsTransformationClaimType используется для копирования локализованных строк в заявки. Для получения дополнительной информации см. [Преобразование заявок GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

В следующем примере показано, как локализовать притязания на выводные данные преобразования требований GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

Значение Predicate используется для локализации одного из сообщений об ошибках [Predicate.](predicates.md) 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Следующий пример показывает, как локализовать предикаты помогают текст.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Входная валидация

Значение ВводаВалиса используется для локализации одного из сообщений ошибок группы [PredicateValidation.](predicates.md) 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

В следующем примере показано, как локализовать текст справки группы справки о проверке предиката.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

Значение UxElement используется для локализации одного из элементов пользовательского интерфейса. В следующем примере показано, как локализовать продолжение и отменить кнопки.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Следующие шаги

Смотрите следующие статьи для примеров локализации:

- [Настройка языка с пользовательской политикой в Azure Active Directory B2C](custom-policy-localization.md)
- [Настройка языка с потоками пользователей в Azure Active Directory B2C](user-flow-language-customization.md)

---
title: Локализация — Azure Active Directory B2C
description: Использование элемента Localization в настраиваемой политике для Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126753"
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

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| Активировано | нет | Возможные значения: `true` или `false`. |

Элемент **Localization** содержит следующие элементы XML:

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Список поддерживаемых языков. |
| LocalizedResources | 0:n | Список локализованных ресурсов. |

## <a name="supportedlanguages"></a>SupportedLanguages

Элемент **SupportedLanguages** содержит следующие атрибуты.

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| DefaultLanguage | Да | Язык, который будет использоваться по умолчанию для локализованных ресурсов. |
| MergeBehavior | нет | Перечисление значений, которые объединяются с любым элементом ClaimType с таким же идентификатором в родительской политике. Используйте этот атрибут, чтобы переопределить определенное в базовой политике утверждение. Возможные значения: `Append`, `Prepend` или `ReplaceAll`. Значение `Append` означает, что указанная коллекция данных должна добавляться в конец коллекции, указанной в родительской политике. Значение `Prepend` означает, что указанная коллекция данных должна добавляться в начало коллекции, указанной в родительской политике. Значение `ReplaceAll` означает, что определенную в родительской политике коллекцию данных нужно игнорировать и заменить данными, определенными в текущей политике. |

### <a name="supportedlanguages"></a>SupportedLanguages

Элемент **SupportedLanguages** содержит следующие элементы:

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Отображает содержимое, которое соответствует тега языка в RFC 5646 (теги для идентификации языков). |

## <a name="localizedresources"></a>LocalizedResources

Элемент **LocalizedResources** содержит следующие атрибуты:

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| Идентификатор | Да | Идентификатор, который используется для уникальной идентификации локализованных ресурсов. |

Элемент **LocalizedResources** содержит следующие элементы:

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Определяет целые коллекции для разных языков и региональных параметров. Коллекция может содержать разное количество элементов и разных строк для разных языков и региональных параметров. Примерами коллекций можно считать перечисления, которые указываются в типах утверждений. Например, содержимое раскрывающегося списка стран или регионов, отображаемое для пользователя. |
| LocalizedStrings | 0:n | Определяет все строки, за исключением включенных в коллекции, для разных языков и региональных параметров. |

### <a name="localizedcollections"></a>LocalizedCollections

Элемент **LocalizedCollections** содержит следующие элементы:

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Список поддерживаемых языков. |

#### <a name="localizedcollection"></a>LocalizedCollection

Элемент **LocalizedCollections** содержит следующие атрибуты:

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| ElementType | Да | Ссылается на элемент ClaimType или элемент пользовательского интерфейса в файле политики. |
| ElementId | Да | Строка, содержащая ссылку на тип утверждения, который уже определен в разделе ClaimsSchema и используется, если **ElementType** имеет значение ClaimType. |
| TargetCollection | Да | Целевая коллекция. |

Элемент **LocalizedCollections** содержит следующие элементы:

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| Элемент | 0:n | Определяет варианты, которые пользователь может выбрать для утверждения в пользовательском интерфейсе, например, значения в раскрывающемся списке. |

Элемент **Item** содержит следующие атрибуты:

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| текст | Да | Понятная для пользователя отображаемая строка, которая размещается в пользовательском интерфейсе для этого параметра. |
| Значение | Да | Строковое значение утверждения, связанное с этим вариантом. |
| SelectByDefault | нет | Указывает, следует ли по умолчанию выбирать этот параметр в пользовательском интерфейсе. Возможные значения: True или False. |

Следующий пример демонстрирует использование элемента **LocalizedCollections**. Он содержит два элемента **LocalizedCollection** по одному для английского и испанского языков. Оба они устанавливают для утверждения **коллекцию**Restriction`Gender` со списком элементов на английском и испанском языках.

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

| Элемент | Вхождения | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Локализованная строка. |

Элемент **LocalizedStrings** содержит следующие атрибуты:

| attribute | Обязательно | Description |
| --------- | -------- | ----------- |
| ElementType | Да | Ссылается на элемент типа утверждения или элемент пользовательского интерфейса в файле политики. Возможные значения: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`или `GetLocalizedStringsTransformationClaimType`. Значение `ClaimType` используется для локализации одного из атрибутов утверждения, который указан в параметре StringId. Значение `UxElement` используется для локализации одного из элементов пользовательского интерфейса, который указан в параметре StringId. Значение `ErrorMessage` используется для локализации одного из системных сообщений об ошибке, которое указано в параметре StringId. Значение `Predicate` используется для локализации одного из сообщений об ошибке [Predicate](predicates.md), которое указано в параметре StringId. Значение `InputValidation` используется для локализации одного из сообщений об ошибке [PredicateValidation](predicates.md), которое указано в параметре StringId. Значение `GetLocalizedStringsTransformationClaimType` используется для копирования локализованных строк в утверждения. Дополнительные сведения см. в разделе [Преобразование утверждений жетлокализедстрингстрансформатион](string-transformations.md#getlocalizedstringstransformation) .  | 
| ElementId | Да | Если **ElementType** имеет значение `ClaimType`, `Predicate` или `InputValidation`, этот элемент содержит ссылку на тип утверждения, которое уже определено в разделе ClaimsSchema. |
| StringId | Да | Если **ElementType** имеет значение `ClaimType`, этот элемент содержит ссылку на атрибут типа утверждения. Возможные значения: `DisplayName`, `AdminHelpText` или `PatternHelpText`. Значение `DisplayName` задает отображаемое имя утверждения. Значение `AdminHelpText` задает имя текста справки для пользователя утверждения. Значение `PatternHelpText` задает текста справки по шаблону утверждения. Если **ElementType** имеет значение `UxElement`, этот элемент содержит ссылку на атрибут элемента пользовательского интерфейса. Если **ElementType** имеет значение `ErrorMessage`, этот элемент задает идентификатор сообщения об ошибке. Полный список идентификаторов [ для строк локализации можно найти на ](localization-string-ids.md)этой странице`UxElement`.|


Следующий пример демонстрирует локализованную страницу регистрации. Первые три значения **LocalizedString** задают атрибут утверждения. Третий из них изменяет значение для кнопки "Продолжить". Последний изменяет сообщение об ошибке.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Следующий пример локализует значение **UserHelpText** в объекте **Predicate** с идентификатором `IsLengthBetween8And64`. Также он локализует **UserHelpText** для **PredicateGroup** с идентификатором `CharacterClasses` в объекте **PredicateValidation** с идентификатором `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Настройка локализации

В этой статье описано, как включить поддержку нескольких языковых стандартов или языков в политике для путей взаимодействия пользователя. Для локализации нужно выполнить три шага: настроить явный список поддерживаемых языков, предоставить строки и коллекции для каждого языка и изменить параметр ContentDefinition для страницы.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Настройка явного списка поддерживаемых языков

В элементе **BuildingBlocks** добавьте элемент **Localization** со списком поддерживаемых языков. В следующем примере показано, как определить поддержку локализации для английского (по умолчанию) и испанского языков:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Дальнейшие действия

Примеры локализации см. в следующих статьях:

- [Настройка языка с помощью настраиваемой политики в Azure Active Directory B2C](custom-policy-localization.md)
- [Настройка языка с помощью потоков пользователя в Azure Active Directory B2C](user-flow-language-customization.md)

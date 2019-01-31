---
title: Элементы Predicates и PredicateValidations — Azure Active Directory B2C | Документация Майкрософт
description: Примеры преобразования утверждений учетной записи социальной сети для схемы инфраструктуры процедур идентификации Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9bd015076cdbd70768b1359fac0cfc893d871513
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149600"
---
# <a name="predicates-and-predicatevalidations"></a>Элементы Predicates и PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элементы **Predicates** и **PredicateValidations** позволяют выполнять процесс проверки, чтобы гарантировать, что только данные в правильном формате вводятся в клиент Azure Active Directory (AAD) B2C.  

На следующей схеме показана связь между этими элементами.  

![Предикаты](./media/predicates/predicates.png)

## <a name="predicates"></a>Предикаты  

Элемент **Predicate** определяет основную проверку (значения типа утверждения) и возвращает `true` или `false`. Проверка выполняется с помощью указанного элемента **Method** и набора соответствующих методу элементов **Parameter**. Например, предикат может проверить, находится ли длина строкового значения утверждения в указанном диапазоне (от минимального до максимального параметра) и содержит ли строковое значение утверждения набор знаков. Элемент **UserHelpText** предоставляет сообщение об ошибке для пользователей при неудачном завершении проверки. Значение элемента **UserHelpText** можно локализовать с помощью [настройки языка](localization.md).

Элемент **Predicates** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| Predicate | 1:n | Список предикатов. | 

Элемент **Predicate** содержит следующие атрибуты.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Идентификатор | Yes | Идентификатор, который используется для предиката. Другие элементы могут использовать этот идентификатор в политике. |
| Метод | Yes | Тип метода для проверки. Возможные значения: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters** или **IsDateRange**. Значение **IsLengthRange** проверяет, находится ли длина строкового значения утверждения в заданном диапазоне (от минимального до максимального параметра). Значение **MatchesRegex** проверяет, соответствует ли строковое значение утверждения регулярному выражению. Значение **IncludesCharacters** проверяет, содержит ли строковое значение утверждения набор знаков. Значение **IsDateRange** проверяет, находится ли значение утверждения даты в заданном диапазоне (от минимального до максимального параметра). |

Элемент **Predicate** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Сообщение об ошибке для пользователей при неудачном завершении проверки. Эту строку можно локализовать с помощью [настройки языка](localization.md). |
| Параметры | 1:1 | Параметры для типа метода проверки строки. | 

Элемент **Parameters** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| Параметр | 1:n | Параметры для типа метода проверки строки. | 

Элемент **Parameter** содержит следующие атрибуты.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| Идентификатор | 1:1 | Идентификатор параметра. |

В следующем примере показан метод `IsLengthRange` с параметрами `Minimum` и `Maximum`, которые указывают диапазон длины строки:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

В следующем примере показан метод `MatchesRegex` с параметром `RegularExpression`, определяющим регулярное выражение:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

В следующем примере показан метод `IncludesCharacters` с параметром `CharacterSet`, определяющим набор знаков:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

В следующем примере показан метод `IsDateRange` с параметрами `Minimum` и `Maximum`, которые указывают диапазон дат в формате `yyyy-MM-dd` и `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

В то время как предикаты определяют проверку на соответствие типу утверждения, **PredicateValidations** группирует набор предикатов для формирования проверки ввода пользователя, соответствующей типу утверждения. Каждый элемент **PredicateValidation** содержит набор элементов **PredicateGroup**, которые, в свою очередь, содержат набор элементов **PredicateReference**, указывающих на **Predicate**. Чтобы пройти проверку, значение утверждения должно пройти все тесты любого предиката по всем **PredicateGroup** с их наборами элементов **PredicateReference**.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Элемент **PredicateValidations** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | Список проверок предиката. | 

Элемент **PredicateValidation** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Идентификатор | Yes | Идентификатор, который используется для проверки предиката. Элемент **ClaimType** может использовать этот идентификатор в политике. |

Элемент **PredicateValidation** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Список групп предикатов. | 

Элемент **PredicateGroups** содержит следующий элемент.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Список предикатов. | 

Элемент **PredicateGroup** содержит следующий атрибут.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Идентификатор | Yes | Идентификатор, который используется для группы предикатов.  |

Элемент **PredicateGroup** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Описание предиката, которое помогает пользователям понять, какое значение следует вводить. | 
| PredicateReferences | 1:n | Список ссылок на предикаты. | 

Элемент **PredicateReferences** содержит следующие атрибуты.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| MatchAtLeast | Нет  | Указывает, какому минимальному количеству определений предикатов значение должно соответствовать, чтобы ввод был принят. |

Элемент **PredicateReferences** содержит следующие элементы.

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | Ссылка на предикат. | 

Элемент **PredicateReference** содержит следующие атрибуты.

| Атрибут | Обязательно | ОПИСАНИЕ |
| --------- | -------- | ----------- |
| Идентификатор | Yes | Идентификатор, который используется для проверки предиката.  |


## <a name="configure-password-complexity"></a>Настройка сложности пароля

С помощью элементов **Predicates** и **PredicateValidationsInput** можно задать требования к сложности паролей пользователей, указываемых при создании учетной записи. По умолчанию Azure AD B2C использует надежные пароли. Azure AD B2C также поддерживает параметры конфигурации для управления сложностью паролей, используемых клиентами. Сложность пароля можно определить с помощью этих элементов предиката: 

- **IsLengthBetween8And64** с помощью метода `IsLengthRange` проверяет, содержит ли пароль от 8 до 64 знаков.
- **Lowercase** с помощью метода `IncludesCharacters` проверяет, содержит ли пароль строчную букву.
- **Uppercase** с помощью метода `IncludesCharacters` проверяет, содержит ли пароль прописную букву.
- **Number** с помощью метода `IncludesCharacters` проверяет, содержит ли пароль цифру.
- **Symbol** с помощью метода `IncludesCharacters` проверяет, содержит ли пароль один из следующих знаков: `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN** с помощью метода `MatchesRegex` проверяет, содержит ли пароль только цифры.
- **AllowedAADCharacters** с помощью метода `MatchesRegex` проверяет, используются ли в пароле только допустимые знаки.
- **DisallowedWhitespace** с помощью метода `MatchesRegex` проверяет, не начинается или не заканчивается ли пароль пробелом.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

После определения базовых проверок можно объединить их и создать набор политик паролей, которые можно использовать в общей политике:

- **SimplePassword** выполняет проверки DisallowedWhitespace, AllowedAADCharacters и IsLengthBetween8And64.
- **StrongPassword** выполняет проверки DisallowedWhitespace, AllowedAADCharacters и IsLengthBetween8And64. Последняя группа `CharacterClasses` выполняет дополнительный набор предикатов с параметром `MatchAtLeast` со значением 3. Пароль пользователя должен содержать от 8 до 16 знаков и три знака следующих типов: строчная, прописная буква, цифра или символ.
- **CustomPassword** выполняет только проверки DisallowedWhitespace и AllowedAADCharacters. То есть пользователь может предоставить пароль любой длины до тех пор, пока знаки являются допустимыми.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

В типе утверждения добавьте элемент **PredicateValidationReference** и укажите идентификатор как одну из проверок предиката, например SimplePassword, StrongPassword или CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Ниже показано, как упорядочены элементы, когда Azure AD B2C отображает сообщение об ошибке:

![Процесс предиката](./media/predicates/predicates-pass.png)

 ## <a name="configure-a-date-range"></a>Настройка диапазона дат

С помощью элементов **Predicates** и **PredicateValidations** вы можете управлять минимальным и максимальным значениями даты типа **UserInputType** с использованием `DateTimeDropdown`. Чтобы сделать это, создайте **Predicate** с методом `IsDateRange` и укажите минимальный и максимальный параметры.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Добавьте **PredicateValidation** со ссылкой на предикат `DateRange`.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

В типе утверждения добавьте элемент **PredicateValidationReference** и укажите идентификатор как `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

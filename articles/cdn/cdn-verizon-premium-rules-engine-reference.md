---
title: Справочник по обработчику правил Azure CDN | Документация Майкрософт
description: Справочная документация по условиям соответствия и функциям обработчика правил Azure CDN.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593150"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN от Verizon уровня "премиум" Справочнике по обработчику правил

В этой статье подробно описаны доступные условия соответствия и компоненты для [обработчика правил](cdn-verizon-premium-rules-engine.md) сети доставки содержимого (CDN) Azure.

Обработчик правил принимает решения о том, как CDN обрабатывает определенные типы запросов.

**С его помощью можно**:

- определять или переопределять пользовательские политики кэширования;
- защищать или отклонять запросы на получение конфиденциального содержимого;
- перенаправлять запросы;
- хранить настраиваемые данные журнала.

## <a name="terminology"></a>Терминология

Правило определяется сочетанием используемых [**условных выражений**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**условий соответствия**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) и [**функций**](cdn-verizon-premium-rules-engine-reference-features.md). Эти элементы выделены на следующем рисунке:

 ![Условие соответствия CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Синтаксис

Методы обработки специальных символов могут отличаться в зависимости от того, как условие соответствия или функция обрабатывает текстовые значения. Условие соответствия или функция может использовать следующие способы обработки текста:

1. [**литеральные значения**](#literal-values);
2. [**значения с подстановочными знаками**](#wildcard-values);
3. [**регулярные выражения**](#regular-expressions).

### <a name="literal-values"></a>Литеральные значения

В тексте, который интерпретируется как литеральное значение, все специальные символы (кроме символа %) рассматриваются как часть сопоставляемого значения. Таким образом, если в условии соответствия по литеральному значению задано значение `\'*'\`, это условие выполняется только при точном совпадении значения (т. е. если найден текст `\'*'\`).

Символ процента обозначает кодировку URL (например, `%20`).

### <a name="wildcard-values"></a>Значения с подстановочными знаками

В тексте, который интерпретируется как значения с подстановочными знаками, определенным символам присваивается дополнительное значение. В следующей таблице описан набор таких символов и правила их интерпретации:

Character | Описание
----------|------------
\ | Обратная косая черта экранирует любой из символов, указанных в этой таблице. Обратная косая черта должна располагаться непосредственно перед тем специальным символом, который нужно экранировать.<br/>Например, в этом примере экранируется символ звездочки: `\*`
% | Символ процента обозначает кодировку URL (например, `%20`).
\* | Звездочка является подстановочным знаком, которому соответствуют один или несколько символов.
Пробел | Символ пробела указывает, что условию соответствия будут удовлетворять любые из указанных значений или шаблонов.
'значение' | Одна одинарная кавычка не имеет специального значения. Но значения, заключенные в набор одинарных кавычек, будут рассматриваться как литеральное значение. Это можно использовать следующими способами.<br><br/>Так можно задать условие соответствия, которому соответствует любая часть оцениваемого значения.  Например, условию `'ma'` будет соответствовать любая из следующих строк: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />Так можно указать, что любой специальный символ должен рассматриваться как литеральный символ. Например, пробел соответствует литеральному символу пробела, если заключить его в парные одинарные кавычки (т. е. `' '` или `'sample value'`).<br/>Так можно указать пустое значение. Чтобы задать в условии пустое значение, используйте набор одинарных кавычек (т. е. '').<br /><br/>**Важно!**<br/>– Если указанное значение не содержит подстановочный знак, оно автоматически считается литеральным значением. В этом случае не требуется использовать набор одинарных кавычек.<br/>– Если обратная косая черта находится между парой одинарных кавычек и не экранирует другой специальный символ, указанный в этой таблице, она игнорируется.<br/>– Чтобы специальный символ рассматривался как литерал, можно также экранировать его с помощью обратной косой черты (т. е. `\`).

### <a name="regular-expressions"></a>Регулярные выражения

Регулярные выражения определяют шаблон, по которому выполняется поиск в текстовом значении. Синтаксис регулярных выражений определяет особые правила для многих символов. В следующей таблице показано, как интерпретируются эти специальные символы в условиях совпадения и функциях, поддерживающих регулярные выражения.

Специальный символ | Описание
------------------|------------
\ | Обратная косая черта экранирует символ, который за ней следует. Такой символ рассматривается как литеральное значение. К нему не применяются специальные правила регулярных выражений. Например, в этом примере экранируется символ звездочки: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdЗначение символа процента зависит от того, как он используется. Docs
descr`%{HTTPVariable}`. Этот синтаксис обозначает переменную HTTP.match`%{HTTPVariable%Pattern}`. Этот синтаксис использует символ процента для идентификации переменную HTTP, а как разделитель.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Звездочка обозначает, что стоящий перед ней символ может присутствовать в оцениваемом значении ноль или более раз.
Пробел | Символ пробела обычно рассматривается как литеральный символ.
'значение' | Одинарные кавычки рассматриваются как литеральные символы. Набор одинарных кавычек не имеет специального значения.

## <a name="next-steps"></a>Следующие шаги

- [Условия соответствия для обработчика правил](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Условные выражения обработчика правил](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Возможности обработчика правил](cdn-verizon-premium-rules-engine-reference-features.md)
- [Переопределение режима HTTP с помощью обработчика правил Azure CDN](cdn-verizon-premium-rules-engine.md)
- [Общие сведения о сети доставки содержимого Azure](cdn-overview.md)
---
title: Синтаксис и выражения шаблона
description: Описывает декларативный синтаксис JSON для шаблонов Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484198"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Синтаксис и выражения в шаблонах Azure Resource Manager

Базовый синтаксис шаблона — это JSON. Однако можно использовать выражения для расширения значений JSON, доступных в шаблоне.  Выражения начинаются и заканчиваются квадратными скобками: `[` и `]` соответственно. Значение выражения вычисляется при развертывании шаблона. Выражение может возвращать строку, целое число, логическое значение, массив или объект.

Выражение шаблона не может содержать более 24 576 символов.

## <a name="use-functions"></a>Использование функций

В следующем примере показано выражение в значении параметра по умолчанию:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

В рамках выражения синтаксис `resourceGroup()` вызывает одну из функций, которые диспетчер ресурсов предоставляет для использования в шаблоне. В этом случае это функция [resourceGroup](template-functions-resource.md#resourcegroup) . Как и в языке JavaScript, вызовы функций форматируются так: `functionName(arg1,arg2,arg3)`. Синтаксис `.location` извлекает одно свойство из объекта, возвращаемого этой функцией.

Функции шаблонов и их параметры зависят от регистра. Например, Resource Manager одинаково преобразует **variables('var1')** и **VARIABLES('VAR1')** . При вычислении функция сохранит регистр, если его изменение не является ее явным предназначением (например, функции toUpper и toLower). Некоторые типы ресурсов могут иметь требования к регистру, которые отделены от того, как оцениваются функции.

Чтобы передать строковое значение в качестве параметра функции, используйте одинарные кавычки.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape-символы

Чтобы литеральная строка начинались с левой скобки `[` и заканчиваться правой квадратной скобкой `]`, но не интерпретировать ее как выражение, добавьте лишнюю скобку, чтобы начать строку с `[[`. Например, переменная:

```json
"demoVar1": "[[test value]"
```

Разрешается в `[test value]`.

Однако если литеральная строка не заканчивается квадратной скобкой, не нужно заключать первую квадратную скобку. Например, переменная:

```json
"demoVar2": "[test] value"
```

Разрешается в `[test] value`.

Для экранирования двойных кавычек в выражении, например для добавления объекта JSON в шаблон, используйте обратную косую черту.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Дальнейшие действия

* Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](template-functions.md).
* Дополнительные сведения о файлах шаблонов см. [в разделе сведения о структуре и синтаксисе шаблонов Azure Resource Manager](template-syntax.md).

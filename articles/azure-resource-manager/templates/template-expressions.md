---
title: Шаблонный синтаксис и выражения
description: Описывает декларативный синтаксис JSON для шаблонов управления ресурсами Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460115"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Синтаксис и выражения в шаблонах управления ресурсами Azure

Базовый синтаксис шаблона — это JSON. Тем не менее, можно использовать выражения для расширения значений JSON, доступных в шаблоне.  Выражения начинаются и заканчиваются квадратными скобками: `[` и `]` соответственно. Значение выражения вычисляется при развертывании шаблона. Выражение может возвращать строку, целое число, логическое значение, массив или объект.

Выражение шаблона не может превышать 24 576 символов.

## <a name="use-functions"></a>Использование функций

Менеджер ресурсов Azure предоставляет [функции,](template-functions.md) которые можно использовать в шаблоне. В следующем примере показано выражение, используюеееееееееееееее функцию в значении параметра по умолчанию:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

В рамках выражения синтаксис `resourceGroup()` вызывает одну из функций, которые диспетчер ресурсов предоставляет для использования в шаблоне. В этом случае это функция [resourceGroup.](template-functions-resource.md#resourcegroup) Как и в языке JavaScript, вызовы функций форматируются так: `functionName(arg1,arg2,arg3)`. Синтаксис `.location` извлекает одно свойство из объекта, возвращенного этой функцией.

Функции шаблонов и их параметры зависят от регистра. Например, Resource Manager одинаково преобразует **variables('var1')** и **VARIABLES('VAR1')**. При вычислении функция сохранит регистр, если его изменение не является ее явным предназначением (например, функции toUpper и toLower). Некоторые типы ресурсов могут иметь требования к случаю, которые отделены от того, как оцениваются функции.

Чтобы передать значение строки в качестве параметра функции, используйте одиночные кавычки.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Большинство функций работают одинаково независимо от того, развернуто ли в группе ресурсов, подписке, группе управления или арендаторе. Следующие функции имеют ограничения, основанные на области:

* [resourceGroup](template-functions-resource.md#resourcegroup) - может использоваться только при развертывании в группе ресурсов.
* [resourceId](template-functions-resource.md#resourceid) - может использоваться в любой сфере, но допустимые параметры меняются в зависимости от сферы.
* [подписка](template-functions-resource.md#subscription) - может быть использована только при развертывании в группе ресурсов или подписке.

## <a name="escape-characters"></a>Escape-символы

Чтобы иметь буквальное строку начать `[` с левой кронштейни и в конце с правой кронштейн, `]`но `[[`не он интерпретируется как выражение, добавить дополнительный кронштейн, чтобы начать строку с . Например, переменная:

```json
"demoVar1": "[[test value]"
```

Разрешает . `[test value]`

Однако, если буквальная строка не заканчивается кронштейном, не избегаете первой кронштейна. Например, переменная:

```json
"demoVar2": "[test] value"
```

Разрешает . `[test] value`

Чтобы избежать двойных кавычек в выражении, таких как добавление объекта JSON в шаблон, используйте backslash.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

При прохождении в значениях параметров использование символов побега зависит от того, где указывается значение параметра. Если вы установите значение по умолчанию в шаблоне, вам понадобится дополнительная левая кронштейна.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Если вы используете значение по `[test value]`умолчанию, шаблон возвращается.

Однако, если вы проходите в параметре значение через командную строку, символы интерпретируются буквально. Развертывание предыдущего шаблона с:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Возвращает `[[test value]`. Вместо этого используйте:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

То же форматирование применяется при передаче значений из файла параметра. Персонажи интерпретируются буквально. При использовании с предыдущим шаблоном `[test value]`возвращается следующий параметр файла:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Значения NULL

Чтобы установить свойство на нуле, вы можете использовать **null** или **«json('null')».** [Функция json](template-functions-array.md#json) возвращает пустой объект `null` при предоставлении в качестве параметра. В обоих случаях шаблоны менеджера ресурсов рассматривают его так, как будто свойства нет.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Дальнейшие действия

* Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](template-functions.md).
* Для получения дополнительной информации о файлах шаблонов [см. Понять структуру и синтаксис шаблонов azure Resource Manager.](template-syntax.md)

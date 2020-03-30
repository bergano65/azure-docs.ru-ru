---
title: Элемент uI TextBox
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.TextBox для портала Azure. Используется для добавления неформатного текста.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652285"
---
# <a name="microsoftcommontextbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.TextBox

Элемент управления, который может использоваться для редактирования неформатированного текста.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>схема

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
"my text value"
```

## <a name="remarks"></a>Remarks

- Если для параметра `constraints.required` задано значение **true**, то текстовое поле должно содержать значение, чтобы пройти проверку. По умолчанию используется значение **false**.
- `constraints.regex` — это шаблон регулярного выражения JavaScript. Если параметр указан, значение текстового поля должно соответствовать шаблону, чтобы пройти проверку. Значение по умолчанию **является нулевым.**
- `constraints.validationMessage` — это строка, которая отображается, когда значение в текстовом поле не проходит проверку. Если параметр не указан, используются встроенные сообщения проверки текстового поля. Значение по умолчанию **является нулевым.**
- Можно указать значение для `constraints.regex`, если для параметра `constraints.required` задано значение **false**. В этом случае, чтобы пройти проверку, значение для текстового поля не требуется. Если указано, оно должно соответствовать шаблону регулярного выражения.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

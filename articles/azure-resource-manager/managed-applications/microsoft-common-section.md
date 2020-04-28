---
title: Элемент пользовательского интерфейса раздела
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.Section для портала Azure. Используйте для группировки элементов на портале для развертывания управляемых приложений.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652259"
---
# <a name="microsoftcommonsection-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.Section

Элемент управления, группирующий один или несколько элементов под заголовком.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>схема

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Remarks

- Параметр `elements` должен содержать по крайней мере один элемент. Этот параметр может содержать элементы любого типа, кроме `Microsoft.Common.Section`.
- Этот элемент не поддерживает свойство `toolTip`.

## <a name="sample-output"></a>Пример выходных данных
Для доступа к выходным значениям элементов в `elements` используйте функции [basics()](create-uidefinition-functions.md#basics) или [steps()](create-uidefinition-functions.md#steps) и точечную нотацию:

```json
steps('configuration').section1.text1
```

Элементы типа `Microsoft.Common.Section` не содержат выходные значения.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

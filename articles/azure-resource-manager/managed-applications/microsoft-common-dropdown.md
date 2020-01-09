---
title: Элемент пользовательского интерфейса DropDown
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.DropDown для портала Azure. Используйте для выбора из доступных параметров при развертывании управляемого приложения.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652389"
---
# <a name="microsoftcommondropdown-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.DropDown

Элемент управления для выбора с раскрывающимся списком.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>схема

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
"two"
```

## <a name="remarks"></a>Remarks

- Метка для `constraints.allowedValues` — это отображаемый текст элемента. Его значение — это выходное значение при выборе элемента.
- Если указано, значение по умолчанию должно быть меткой в `constraints.allowedValues`. Если не указано, выбирается первый элемент в `constraints.allowedValues`. Значение по умолчанию — **null**.
- В `constraints.allowedValues` должен содержаться по крайней мере один элемент.
- Чтобы эмулировать значение, которое не требуется, добавьте элемент с меткой и значением `""` (пустая строка) в `constraints.allowedValues`.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

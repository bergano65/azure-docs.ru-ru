---
title: Элемент пользовательского интерфейса DropDown для портала Azure | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.DropDown для портала Azure. Используйте для выбора из доступных параметров при развертывании управляемого приложения.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5784b5df9b522f0489ca1f6087627b45cec5349d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331738"
---
# <a name="microsoftcommondropdown-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.DropDown
Элемент управления для выбора с раскрывающимся списком.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса
![Элемент пользовательского интерфейса Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>SCHEMA (Схема)
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

## <a name="remarks"></a>Примечания

- Метка для `constraints.allowedValues` — это отображаемый текст элемента. Его значение — это выходное значение при выборе элемента.
- Если указано, значение по умолчанию должно быть меткой в `constraints.allowedValues`. Если не указано, выбирается первый элемент в `constraints.allowedValues`. Значение по умолчанию — **null**.
- В `constraints.allowedValues` должен содержаться по крайней мере один элемент.
- Чтобы эмулировать значение, которое не требуется, добавьте элемент с меткой и значением `""` (пустая строка) в `constraints.allowedValues`.

## <a name="sample-output"></a>Пример выходных данных
```json
"two"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

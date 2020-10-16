---
title: Элемент пользовательского интерфейса CheckBox
description: Описывает элемент пользовательского интерфейса Microsoft. Common. CheckBox для портал Azure. Позволяет пользователям выбрать параметр, чтобы установить или снять флажок.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098544"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft. Common. CheckBox

Элемент управления CheckBox позволяет пользователям устанавливать или отменять выбор параметра. Элемент управления возвращает **значение true** , если элемент управления установлен, или **значение false** , если флажок не установлен.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>схема

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Пример полученных результатов

```json
true
```

## <a name="remarks"></a>Remarks

Если для параметра **обязательно** задано **значение true**, пользователь должен установить флажок. Если пользователь не выберет флажок, выводится сообщение проверки.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

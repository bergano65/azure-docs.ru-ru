---
title: Элемент пользовательского интерфейса PasswordBox
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.PasswordBox для портала Azure. Позволяет пользователям предоставлять секретные значения при развертывании управляемых приложений.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7902ea2e30dec20e57d88344dc9507aec3993600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87064079"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.PasswordBox

Элемент управления, который может использоваться для указания и подтверждения пароля.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft-common-passwordbox.png)

## <a name="schema"></a>схема

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Пример полученных результатов

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Remarks

- Этот элемент не поддерживает свойство `defaultValue`.
- Дополнительные сведения о реализации `constraints` см. в статье [Элемент пользовательского интерфейса Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Если для параметра `options.hideConfirmation` задано значение **true**, второе текстовое поле для подтверждения пароля скрыто. Значение по умолчанию — **false**.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

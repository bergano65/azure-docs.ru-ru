---
title: Элемент пользовательского интерфейса TextBox
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.TextBox для портала Azure. Используется для добавления неформатированного текста.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474314"
---
# <a name="microsoftcommontextbox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.TextBox

Элемент управления, который может использоваться для редактирования неформатированного текста.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Схема

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
"contoso123"
```

## <a name="remarks"></a>Комментарии

- Если для параметра `constraints.required` задано значение **true**, то текстовое поле должно содержать значение, чтобы пройти проверку. Значение по умолчанию — **false**.
- Свойство представляет собой массив, в который `validations` добавляются условия для проверки значения, указанного в текстовом поле.
- `regex`Свойство является шаблоном регулярного выражения JavaScript. Если параметр указан, значение текстового поля должно соответствовать шаблону, чтобы пройти проверку. Значение по умолчанию — **null**.
- `isValid`Свойство содержит выражение, результатом которого является значение true или false. В рамках выражения определяется условие, определяющее, является ли текстовое поле допустимым.
- `message`Свойство является строкой, отображаемой, когда значение текстового поля не проходит проверку.
- Можно указать значение для `regex`, если для параметра `required` задано значение **false**. В этом случае, чтобы пройти проверку, значение для текстового поля не требуется. Если указано, оно должно соответствовать шаблону регулярного выражения.
- `placeholder`Свойство — это текст справки, который исчезает, когда пользователь начинает редактирование. Если `placeholder` определены и `defaultValue` , то `defaultValue` имеет приоритет и отображается.

## <a name="example"></a>Пример

В следующем примере для проверки доступности имени ресурса используется текстовое поле с элементом управления [Microsoft. Solutions. армапиконтрол](microsoft-solutions-armapicontrol.md) .

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

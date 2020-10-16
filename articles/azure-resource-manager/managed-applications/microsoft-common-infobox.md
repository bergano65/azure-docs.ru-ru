---
title: Элемент пользовательского интерфейса Инфобокс
description: Описывает элемент пользовательского интерфейса Microsoft. Common. Инфобокс для портал Azure. Используйте для добавления текста или предупреждений при развертывании управляемого приложения.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033362"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.InfoBox

Элемент управления, который добавляет поле сведений. Поле содержит важный текст или предупреждения, которые помогают пользователям понять, какие значения нужно указать. Он также может быть связан с URI, по которому можно получить более подробную информацию.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>схема

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Пример полученных результатов

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Remarks

* Установите для параметра `icon` одно из следующих значений: **Нет**, **Сведения**, **Предупреждение** или **Ошибка**.
* Свойство `uri` необязательное.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

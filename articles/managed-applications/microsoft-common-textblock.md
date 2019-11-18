---
title: Элемент пользовательского интерфейса Azure TextBlock | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Common.TextBlock для портала Azure. Используйте для добавления текста в интерфейс.
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
ms.openlocfilehash: 1dbef1e43d9f970665a31f0e4e9270d3193edaba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151734"
---
# <a name="microsoftcommontextblock-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Common.TextBlock

Элемент управления, с помощью которого можно добавить текст в интерфейс портала.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>SCHEMA (Схема)

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).

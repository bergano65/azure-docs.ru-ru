---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878257"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Следующие свойства доступны `extensionBundle`в:

| Свойство | Описание |
| -------- | ----------- |
| идентификатор | Пространство имен для пакетов расширения Microsoft Azure. |
| version | Версия расслоения для установки. Время выполнения функций всегда выбирает максимально допустимую версию, определяемую диапазоном или интервалом версии. Значение версии выше позволяет всем версиям пакета от 1.0.0 до но не включая 2.0.0. Для получения дополнительной [interval notation for specifying version ranges](/nuget/reference/package-versioning#version-ranges)информации см. |

Приращение комплекта версий в виде пакетов в изменении пакета. Основные изменения версии происходят, когда пакеты в пакете приращения основной версии. Основные изменения версии в пакете обычно совпадают с изменением основной версии времени выполнения функций.  

Текущий набор расширений, установленных пакетом по умолчанию, перечислен в этом [файле extensions.json.](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)

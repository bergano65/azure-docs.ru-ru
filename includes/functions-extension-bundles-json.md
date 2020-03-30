---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381869"
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

Текущий набор расширений, установленных пакетом по умолчанию, перечислен в этом [файле extensions.json.](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)

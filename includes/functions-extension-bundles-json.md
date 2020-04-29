---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
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

В `extensionBundle`доступны следующие свойства:

| Свойство | Описание |
| -------- | ----------- |
| идентификатор | Пространство имен для пакетов расширений функций Microsoft Azure. |
| Версия | Версия устанавливаемого пакета. Среда выполнения функций всегда выбирает максимально допустимую версию, определенную диапазоном версий или интервалом. Приведенное выше значение версии разрешает все версии пакета от 1.0.0 до, но не включает 2.0.0. Дополнительные сведения см. в описании [интервала для указания диапазонов версий](/nuget/reference/package-versioning#version-ranges). |

Версии пакета увеличиваются по мере изменения пакетов в пакете. Основные изменения версий происходят, когда пакеты в пакете увеличиваются по основной версии. Изменения основной версии в пакете обычно совпадают с изменениями в основной версии среды выполнения функций.  

Текущий набор расширений, установленных пакетом по умолчанию, перечислен в этом [файле Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

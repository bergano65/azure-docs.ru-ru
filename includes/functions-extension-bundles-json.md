---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129069"
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

| Свойство | ОПИСАНИЕ |
| -------- | ----------- |
| id | Пространство имен для пакетов расширений функций Microsoft Azure. |
| версия | Версия устанавливаемого пакета. Среда выполнения функций всегда выбирает максимально допустимую версию, определенную диапазоном версий или интервалом. Приведенное выше значение версии разрешает все версии пакета от 1.0.0 до, но не включает 2.0.0. Дополнительные сведения см. в описании [интервала для указания диапазонов версий](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Версии пакета увеличиваются по мере изменения пакетов в пакете. Основные изменения версий происходят, когда пакеты в пакете увеличиваются по основной версии. Изменения основной версии в пакете обычно совпадают с изменениями в основной версии среды выполнения функций.  

Текущий набор расширений, установленных пакетом по умолчанию, перечислен в этом [файле Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

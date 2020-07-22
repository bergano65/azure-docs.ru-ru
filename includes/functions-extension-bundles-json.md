---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
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

В доступны следующие свойства `extensionBundle` :

| Свойство. | Описание |
| -------- | ----------- |
| идентификатор | Пространство имен для пакетов расширений функций Microsoft Azure. |
| version | Версия устанавливаемого пакета. Среда выполнения функций всегда выбирает максимально допустимую версию, определенную диапазоном версий или интервалом. Приведенное выше значение версии разрешает все версии пакета от 1.0.0 до, но не включает 2.0.0. Дополнительные сведения см. в описании [интервала для указания диапазонов версий](/nuget/reference/package-versioning#version-ranges). |

Версии пакета увеличиваются по мере изменения пакетов в пакете. Основные изменения версий происходят, когда пакеты в пакете увеличиваются по основной версии. Изменения основной версии в пакете обычно совпадают с изменениями в основной версии среды выполнения функций.  

Текущий набор расширений, установленных пакетом по умолчанию, перечислен в этом [extensions.jsв файле](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

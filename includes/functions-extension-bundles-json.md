---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
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

В `extensionBundle` доступны следующие свойства:

| Свойство | Описание |
| -------- | ----------- |
| идентификатор | Пространство имен для наборов расширений Функций Microsoft Azure. |
| version | Версия устанавливаемого пакета. В среде выполнения Функций всегда выбирается максимально допустимая версия, определенная в диапазоне версий. Приведенное выше значение версии допускает использование всех версий пакета от 1.0.0 до 2.0.0 (не включительно). Дополнительные сведения см. в разделе об [интервальной нотации для указания диапазонов версий](/nuget/reference/package-versioning#version-ranges). |

Номера версий наборов увеличиваются по мере изменения пакетов в наборе. Основной номер версии набора изменяется, когда увеличивается номер основной версии пакетов в наборе. Изменения основной версии набора обычно совпадают с изменениями в основной версии среды выполнения Функций.  

Текущий набор расширений, установленных набором по умолчанию, перечислен в файле [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

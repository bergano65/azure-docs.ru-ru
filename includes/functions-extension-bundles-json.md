---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689573"
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
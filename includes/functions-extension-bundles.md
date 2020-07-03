---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68639150"
---
Самый простой способ установить расширения привязки — включить [пакеты расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles). При этом автоматически устанавливается набор стандартных пакетов расширений.

Чтобы включить пакеты расширений, откройте файл host.json и обновите его содержимое так, чтобы оно соответствовало следующему коду:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```
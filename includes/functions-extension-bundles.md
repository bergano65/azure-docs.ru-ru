---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442280"
---
Самый простой способ установить расширения привязки — включить [пакеты расширений](../articles/azure-functions/functions-bindings-register.md#extension-bundles). При этом автоматически устанавливается набор стандартных пакетов расширений.

Чтобы включить пакеты расширений, откройте файл *host.json* и обновите его содержимое так, чтобы оно соответствовало следующему коду:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```
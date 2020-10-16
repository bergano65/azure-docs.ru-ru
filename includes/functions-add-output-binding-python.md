---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191080"
---
Обновите *HttpExample\\\_\_init\_\_.py* в соответствии с кодом ниже, добавив параметр `msg` в определение функции и `msg.set(name)` для оператора `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Параметр `msg` — это экземпляр [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Его метод `set` записывает строковое сообщение в очередь. В этом случае имя передается функции в строке запроса URL-адреса.

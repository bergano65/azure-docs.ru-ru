---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964258"
---
## <a name="update-the-function"></a>Обновление функции

По умолчанию шаблон создает функцию, которая при составлении запросов требует ключ функции. Для упрощения тестирования функции в Azure необходимо обновить функцию, чтобы разрешить анонимный доступ. Способ внесения этого изменения зависит от языка проекта функции.

### <a name="c"></a>C\#

Откройте файл кода MyHttpTrigger.cs (новую функцию) и обновите атрибут **AuthorizationLevel** в определении функции до значения `Anonymous` и сохраните изменения.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Откройте файл function.json для новой функции, откройте его в текстовом редакторе, обновите свойство **authLevel** в **bindings.httpTrigger** до `anonymous` и сохраните изменения.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Теперь функцию в Azure можно вызвать без необходимости указания ключа функции. Ключ функции никогда не требуется при локальном выполнении.

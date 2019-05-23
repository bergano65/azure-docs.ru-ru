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
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132182"
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

Откройте файл function.json для новой функции в текстовом редакторе, обновите свойство **authLevel** в **bindings** до `anonymous` и сохраните изменения.

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

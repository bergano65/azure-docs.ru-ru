---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988025"
---
## <a name="update-the-function"></a>Обновление функции

По умолчанию шаблон создает функцию, которая при составлении запросов требует ключ функции. Для упрощения тестирования функции в Azure необходимо обновить функцию, чтобы разрешить анонимный доступ. Способ внесения этого изменения зависит от языка проекта функции.

### <a name="c"></a>C\#

Откройте файл кода MyHttpTrigger.cs (новую функцию) и обновите атрибут **AuthorizationLevel** в определении функции до значения `anonymous` и сохраните изменения.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
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

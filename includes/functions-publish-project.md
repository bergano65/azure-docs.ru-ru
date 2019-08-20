---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949987"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

Создав в Azure приложение-функцию, вы можете развернуть код проекта в Azure с помощью команды Core Tools [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment). В этом примере замените `<APP_NAME>` на имя приложения, созданного на предыдущем этапе.

### <a name="c--javascript"></a>C\#, JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Появится примерно такой результат (сокращен для удобства чтения):

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Скопируйте значение `Invoke url` для `HttpTrigger`, с помощью которого теперь можно будет протестировать функцию в Azure. URL-адрес содержит значение строки запроса `code`, которое является ключом функции. Если вы используете этот ключ, другим пользователям будет сложно вызвать конечную точку триггера HTTP в Azure.
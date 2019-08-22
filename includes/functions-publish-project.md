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
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562952"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

Создав в Azure приложение-функцию, вы можете развернуть код проекта в Azure с помощью команды Core Tools [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment). В этом примере замените `<APP_NAME>` на имя приложения, созданного на предыдущем этапе.

### <a name="c--javascript"></a>C\#, JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
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
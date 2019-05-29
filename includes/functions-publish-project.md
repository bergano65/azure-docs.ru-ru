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
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132260"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

Создав в Azure приложение-функцию, вы можете развернуть код проекта в Azure с помощью команды Core Tools [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment). В приведенной ниже команде замените `<APP_NAME>` именем приложения, созданного на предыдущем этапе.

```bash
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

Скопируйте значение URL-адреса для вызова триггера HttpTrigger, с помощью которого теперь можно будет протестировать функцию в Azure. URL-адрес содержит значение строки запроса `code`, которое является ключом функции. Если вы используете этот ключ, другим пользователям будет сложно вызвать конечную точку триггера HTTP в Azure.
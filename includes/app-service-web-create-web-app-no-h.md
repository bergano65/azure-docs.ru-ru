---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f7c82a2bd35f06da096fa0dd9d5d1fa4d08011e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838934"
---
В Cloud Shell создайте [веб-приложение](../articles/app-service/overview.md) в рамках плана службы приложений `myAppServicePlan`. Для этого воспользуйтесь командой [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). В следующем примере замените  *\<имя_приложения >* глобально уникальным именем приложения (допустимые символы: `a-z`, `0-9`, и `-`).

```azurecli-interactive
az webapp create --name <app-name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

После создания веб-приложения в Azure CLI отображаются следующие сведения:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение с включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

Перейдите к только что созданному веб-приложению.

```
http://<app-name>.azurewebsites.net
```

Новое веб-приложение должно выглядеть так:

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
ms.openlocfilehash: 6725766ea761a93511d719f883b31821b105d61f
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "58115739"
---
В Cloud Shell создайте [веб-приложение](../articles/app-service/overview.md) в рамках плана службы приложений `myAppServicePlan`. Для этого воспользуйтесь командой [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). В следующем примере замените *\<app_name>* глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`).

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

После создания веб-приложения в Azure CLI отображаются следующие сведения:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение с включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

Перейдите к только что созданному веб-приложению.

```
http://<app_name>.azurewebsites.net
```

Новое веб-приложение должно выглядеть так:

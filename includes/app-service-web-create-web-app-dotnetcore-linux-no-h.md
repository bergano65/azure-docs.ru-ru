---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/31/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3adf751c64f4e19c9a77ccee7d29333c300dab6f
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512607"
---
Создайте [веб-приложение](../articles/app-service/containers/app-service-linux-intro.md) в плане службы приложений `myAppServicePlan`. 

В Cloud Shell можно использовать команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). В следующем примере замените `<app_name>`глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`). Для среды выполнения установлено значение `DOTNETCORE|2.1`. Список всех поддерживаемых сред выполнения можно получить с помощью команды [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "DOTNETCORE|2.1" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "DOTNETCORE|2.1" --deployment-local-git
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

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

Вы создали пустое веб-приложение в контейнере Linux со включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

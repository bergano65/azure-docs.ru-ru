---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 2dc35727a82459750b4414b6e3eeddf28b31b718
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824742"
---
Создайте [веб-приложение](../articles/app-service/containers/app-service-linux-intro.md) в плане службы приложений `myAppServicePlan`. 

В Cloud Shell можно использовать команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). В следующем примере замените `<app-name>`глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`). Для среды выполнения установлено значение `DOTNETCORE|2.2`. Список всех поддерживаемых сред выполнения можно получить с помощью команды [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.2" --deployment-local-git
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

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

Вы создали пустое веб-приложение в контейнере Linux со включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

---
title: Создание приложения Docker или Go в Linux — Служба приложений Azure
description: Как развернуть пользовательский образ Docker с запущенным приложением Go в Веб-приложении для контейнеров.
keywords: azure app service, web app, go, docker, container
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ec2b974e008ea4c7e266f5ae0d46cd67d2133e54
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853999"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>Запуск пользовательского контейнера Linux в Службе приложений Azure

[Служба приложений в Linux](app-service-linux-intro.md) предоставляет предопределенные стеки приложений на платформе Linux с поддержкой определенных языков (например, .NET, PHP, Node.js и т. д.). Вы также можете использовать пользовательский образ Docker для запуска веб-приложения в стеке приложений, который еще не определен в Azure. В этом кратком руководстве показано, как создать веб-приложение и развернуть образ Go из центра Docker. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Пример приложения, выполняющегося в Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Создание веб-приложения

Создайте [веб-приложение](../overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Замените `<app name>` глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`).

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

В предыдущей команде `--deployment-container-image-name` указывает на общедоступный образ центра Docker [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Переход в приложение

```bash
http://<app_name>.azurewebsites.net/hello
```

![Пример приложения, выполняющегося в Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Поздравляем!** Вы развернули пользовательский образ Docker в Веб-приложении Azure для контейнеров.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. по развертыванию из частного репозитория контейнеров](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Настройка пользовательского контейнера](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Руководство. по приложению WordPress с несколькими контейнерами](tutorial-multi-container-app.md)

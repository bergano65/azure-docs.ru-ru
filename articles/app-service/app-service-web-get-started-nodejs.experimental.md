---
title: Создание веб-приложения Node.js в Службе приложений Azure | Документация Майкрософт
description: Быстрое развертывание первого приложения Hello World на Node.js в веб-приложении службы приложений Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 237f498d1ebe2b402c86f1a4aed66a7ed443ccfa
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653679"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Создание веб-приложений Node.js в Azure

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть приложение .NET Core в службе приложений на платформе _Linux_, см. статью [Создание веб-приложения Node.js в службе приложений Azure на платформе Linux](./containers/quickstart-nodejs.md).
>

[Служба приложений Azure](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости.  В этом кратком руководстве объясняется, как развертывать приложения Node.js в Службе приложений Azure. Веб-приложение создается в [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), но эти же команды можно выполнить локально в [Azure CLI](/cli/azure/install-azure-cli). Пример кода Node.js развертывается в веб-приложение с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

![Пример приложения, выполняющегося в Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Выполните приведенные здесь инструкции с помощью компьютера Mac, Windows или Linux. Выполнение всех инструкций занимает около трех минут.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В Cloud Shell создайте каталог quickstart и перейдите в него.

```azurecli-interactive
mkdir quickstart

cd quickstart
```

Затем выполните следующую команду, чтобы клонировать репозиторий с примером приложения в локальный каталог quickstart.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

При выполнении эта команда выводит приблизительно следующие сведения:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> В примере переменная index.js задает порт прослушивания для process.env.PORT. Эта переменная среды назначается Службой приложений.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Создание веб-приложения

В Cloud Shell создайте веб-приложение в рамках плана службы приложений `myAppServicePlan` с помощью команды [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

В следующем примере замените `<app_name>`глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Настройка среды выполнения Node.js

Задайте для среды выполнения Node.js значение 10.14.1. Список всех поддерживаемых сред выполнения можно получить с помощью команды [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes).

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Перейдите к только что созданному веб-приложению. Замените `<app_name>` уникальным именем приложения.

```
http://<app_name>.azurewebsites.net
```

Новое веб-приложение должно выглядеть так: ![Страница пустого веб-приложения](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Развертывание ZIP-файла

В Cloud Shell перейдите в корневой каталог приложения и создайте ZIP-файл для примера проекта.

```azurecli-interactive
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Разверните ZIP-файл в веб-приложение с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Эта команда позволяет развертывать файлы и каталоги из ZIP-файлов в папку для приложения службы приложений по умолчанию (`\home\site\wwwroot`). Затем приложение перезапускается. Если настроен любой дополнительный пользовательский процесс сборки, он также запустится. Дополнительные сведения см. в [документации по Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```
http://<app_name>.azurewebsites.net
```

Пример кода Node.js выполняется в веб-приложении службы приложений Azure.

![Пример приложения, выполняющегося в Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> В Службе приложений Azure приложение запускается в IIS с помощью [iisnode](https://github.com/Azure/iisnode). Чтобы разрешить приложению запускаться с помощью iisnode, корневой каталог приложения должен содержать файл web.config. Файл доступен для чтения в IIS. Связанные параметры iisnode описаны в [репозитории GitHub iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Поздравляем!** Вы развернули свое первое приложение Node.js в службе приложений.

## <a name="update-and-redeploy-the-code"></a>Обновление и повторное развертывание кода

В Cloud Shell введите `code index.js`, чтобы открыть текстовый редактор Cloud Shell.

![Введите код index.js.](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Внесите небольшое изменение в текст вызова метода `response.end`:

```javascript
response.end("Hello Azure!");
```

Сохраните изменения и выйдите из редактора. Выполните команду `^S`, чтобы сохранить файл, и `^Q` — чтобы выйти.

Создайте ZIP-файл и разверните его с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Обновленный пример приложения, выполняющегося в Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Управление новым приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> для управления созданным веб-приложением.

В меню слева щелкните **Службы приложений**, а затем — имя своего приложения Azure.

![Переход к приложению Azure на портале](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

![Страница службы приложений на портале Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

В меню слева доступно несколько страниц для настройки приложения.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Node.js с MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

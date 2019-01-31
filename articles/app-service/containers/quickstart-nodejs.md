---
title: Создание приложения Node.js в Службе приложений Azure в Linux | Документация Майкрософт
description: Быстрое развертывание первого приложения Hello World на Node.js в службе приложений Azure в Linux.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/20/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c06e60b5f7dce3038706e0b95ec598126526cc64
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099181"
---
# <a name="create-a-nodejs-app-in-azure-app-service-on-linux"></a>Создание приложения Node.js в Службе приложений Azure в Linux

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Linux. Сведения о развертывании в Службе приложений на платформе _Windows_ см. в статье [Создание веб-приложений Node.js в Azure](../app-service-web-get-started-nodejs.md).
>

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом кратком руководстве показано, как развернуть приложение Node.js в службе приложений для Linux с помощью [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Действия в этом руководстве выполняются в Cloud Shell, но эти же команды можно выполнить локально в [Azure CLI](/cli/azure/install-azure-cli).

![Пример приложения, выполняющегося в Azure](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В Cloud Shell создайте каталог quickstart и перейдите в него.

```bash
mkdir quickstart

cd quickstart
```

Затем выполните следующую команду, чтобы клонировать репозиторий с примером приложения в локальный каталог quickstart.

```bash
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

## <a name="create-a-web-app"></a>Создание веб-приложения

Перейдите в каталог, в котором содержится пример кода, и выполните команду `az webapp up`.

В следующем примере замените <app_name> уникальным именем приложения.

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

Команда `az webapp up` выполняет следующие действия:

- создание группы ресурсов по умолчанию;

- создание плана службы приложений по умолчанию;

- создание приложения с указанным именем.

- [Разверните ZIP-файлы](https://docs.microsoft.com/azure/app-service/deploy-zip) для приложения из текущего рабочего каталога.

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера. Замените <app_name> именем своего приложения.

```bash
http://<app_name>.azurewebsites.net
```

Пример кода Node.js выполняется в Службе приложений в Linux со встроенным образом.

![Пример приложения, выполняющегося в Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Поздравляем!** Вы развернули свое первое приложение Node.js в службе приложений в Linux.

## <a name="update-and-redeploy-the-code"></a>Обновление и повторное развертывание кода

В Cloud Shell введите `nano index.js`, чтобы открыть текстовый редактор Nano.

![Файл index.js в редакторе Nano](media/quickstart-nodejs/nano-indexjs.png)

 Внесите небольшое изменение в текст вызова метода `response.end`:

```nodejs
response.end("Hello Azure!");
```

Сохраните изменения и выйдите из редактора Nano. Выполните команду `^O`, чтобы сохранить файл, и `^X` — чтобы выйти.

Теперь можно повторно развернуть приложение. Замените `<app_name>` именем своего приложения.

```bash
az webapp up -n <app_name>
```

После завершения развертывания перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Обновленный пример приложения, выполняющегося в Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Управление новым приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>, чтобы управлять созданным приложением.

В меню слева щелкните **Службы приложений**, а затем — имя своего приложения Azure.

![Переход к приложению Azure на портале](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Отобразится страница обзора вашего приложения. Здесь вы можете выполнять базовые задачи управления, например просмотр, завершение, запуск, перезапуск и удаление.

![Страница службы приложений на портале Azure](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

В меню слева доступно несколько страниц для настройки приложения.

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если вы считаете, что в будущем эти ресурсы вам не понадобятся, удалите группу ресурсов через Cloud Shell. Если вы изменили регион, замените имя группы ресурсов `appsvc_rg_Linux_CentralUS` тем именем, которое используется в вашем приложении.

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

Ее выполнение может занять до минуты.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Node.js с MongoDB](tutorial-nodejs-mongodb-app.md)

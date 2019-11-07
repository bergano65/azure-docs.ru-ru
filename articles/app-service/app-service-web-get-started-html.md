---
title: Создание статического веб-приложения HTML в Службе приложений Azure | Документация Майкрософт
description: Узнайте, как запускать веб-приложения в службе приложений Azure, развернув пример статического HTML-приложения.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 12c034e6521a1afd25e2390502faec39461bf5b5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471483"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Создание статического веб-приложения HTML в Azure

[Служба приложений Azure](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом кратком руководстве объясняется, как развернуть простейший сайт HTML+CSS в Служба приложений Azure. Действия в этом руководстве выполняются в [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), но эти же команды можно выполнить локально в [Azure CLI](/cli/azure/install-azure-cli).

![Домашняя страница в примере приложения](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В Cloud Shell создайте каталог quickstart и перейдите в него.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Затем выполните следующую команду, чтобы клонировать репозиторий с примером приложения в локальный каталог quickstart.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Создание веб-приложения

Перейдите в каталог, в котором содержится пример кода, и выполните команду `az webapp up`.

В следующем примере замените <app_name> уникальным именем приложения.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name>
```

Команда `az webapp up` выполняет следующие действия:

- создание группы ресурсов по умолчанию;

- создание плана службы приложений по умолчанию;

- создание приложения с указанным именем.

- [развертывание ZIP-файлов](https://docs.microsoft.com/azure/app-service/deploy-zip) для веб-приложения из текущего рабочего каталога.

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:

```json
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "westeurope",
  "name": "<app_name>",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/<username>/quickstart/html-docs-hello-world ",
  < JSON data removed for brevity. >
}
```

Запишите значение `resourceGroup`. Оно потребуется при выполнении задач в разделе об [очистке ресурсов](#clean-up-resources).

## <a name="browse-to-the-app"></a>Переход в приложение

В браузере перейдите по URL-адресу приложения: `http://<app_name>.azurewebsites.net`.

Страница выполняется как веб-приложение службы приложений Azure.

![Домашняя страница примера приложения](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Поздравляем!** Вы развернули свое первое HTML-приложение в службе приложений.

## <a name="update-and-redeploy-the-app"></a>Обновление и повторное развертывание приложения

В Cloud Shell введите `nano index.html`, чтобы открыть текстовый редактор Nano. В теге заголовка `<h1>` измените запись "Azure App Service - Sample Static HTML Site" (Служба приложений Azure — пример статического HTML-сайта) на "Azure App Service" (Служба приложений Azure), как показано ниже.

![Файл index.html в редакторе Nano](media/app-service-web-get-started-html/nano-index-html.png)

Сохраните изменения и выйдите из редактора Nano. Выполните команду `^O`, чтобы сохранить файл, и `^X` — чтобы выйти.

Теперь повторно разверните приложение с помощью той же команды `az webapp up`.

```bash
az webapp up --location westeurope --name <app_name>
```

После завершения развертывания перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Обновленная домашняя страница примера приложения](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Управление новым приложением Azure

Чтобы управлять созданным веб-приложением, на [портале Azure](https://portal.azure.com) найдите в поиске и выберите **Службы приложений**. 

![Выбор службы приложений на портале Azure](./media/app-service-web-get-started-html/portal0.png)

На странице **Службы приложений** выберите имя приложения Azure.

![Переход к приложению Azure на портале](./media/app-service-web-get-started-html/portal1.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

![Колонка службы приложений на портале Azure](./media/app-service-web-get-started-html/portal2.png)

В меню слева доступно несколько страниц для настройки приложения.

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов, выполнив приведенную ниже команду в Cloud Shell. Помните, что имя группы ресурсов автоматически создано на этапе [создания веб-приложения](#create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Ее выполнение может занять до минуты.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сопоставление пользовательского домена](app-service-web-tutorial-custom-domain.md)

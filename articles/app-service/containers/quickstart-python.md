---
title: Краткое руководство. Создание приложения Python в Службе приложений Azure в Linux
description: Быстрое развертывание первого приложения Hello World на Python в Службе приложений Azure на платформе Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: true
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: e8ca84f233b3e6202a4647d15e07b36c2b8f1128
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433096"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Краткое руководство. Создание приложения Python в Службе приложений Azure в Linux

В этой статье вы развернете простое приложение Python в [Службе приложений в Linux](app-service-linux-intro.md), которая предоставляет высокомасштабируемую веб-службу размещения с самостоятельной установкой исправлений. В руководстве используется интерфейс командной строки Azure ([Azure CLI](/cli/azure/install-azure-cli)) в интерактивной браузерной оболочке Azure Cloud Shell. Поэтому все инструкции можно выполнять на компьютере Mac, Linux или Windows.

![Запуск примера приложения Python в Службе приложений в Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* <a href="https://www.python.org/downloads/" target="_blank">установите Python 3.7</a>;
* <a href="https://git-scm.com/" target="_blank">установите Git</a>;
* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.

## <a name="download-the-sample-locally"></a>Скачать пример на локальный компьютер

В окне терминала выполните нижеприведенные команды, чтобы клонировать пример приложения на локальный компьютер, и перейдите в каталог, содержащий пример кода.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Репозиторий содержит файл *application.py*, который сообщает Службе приложений, что репозиторий содержит приложение Flask. Дополнительные сведения см. в статье [Настройка приложений Python для Службы приложений Azure под управлением Linux](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Локальный запуск приложения

Запустите приложение локально, чтобы увидеть, как оно будет выглядеть после развертывания в Azure. Откройте окно терминала и используйте нижеприведенные команды для установки необходимых зависимостей и запуска встроенного сервера разработки. 

```bash
# In Bash (for Linux or Mac)
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run
```
```powershell
# In Powershell (for Windows)
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Откройте веб-браузер и перейдите к примеру приложения по адресу `http://localhost:5000/`.

На странице отобразится сообщение **Hello World!** из примера приложения.

![Локальный запуск примера приложения Python](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

В окне терминала нажмите клавиши **CTRL+C**, чтобы выйти из веб-сервера.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В Cloud Shell создайте каталог quickstart и перейдите в него.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Затем выполните следующую команду, чтобы клонировать репозиторий с примером приложения в локальный каталог quickstart.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

При выполнении эта команда выводит приблизительно следующие сведения:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Создание веб-приложения

Перейдите в каталог, в котором содержится пример кода, и выполните команду `az webapp up`.

В следующем примере замените `<app-name>` глобальным уникальным именем приложения (*допустимые символы: `a-z`, `0-9` и `-`* ).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Переход в приложение

Перейдите в развертываемое приложение с помощью веб-браузера.

```bash
http://<app-name>.azurewebsites.net
```

Пример кода Python выполняется в Службе приложений в Linux со встроенным образом.

![Запуск примера приложения Python в Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Поздравляем!** Вы развернули свое первое приложение Python в службе приложений в Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Обновление на локальном компьютере и повторное развертывание кода

В Cloud Shell введите `code application.py`, чтобы открыть текстовый редактор Cloud Shell.

![Открытие файла application.py в редакторе Cloud Shell](./media/quickstart-python/open-application-py-in-the-cloud-shell-editor.png)

 Внесите небольшое изменение в текст вызова метода `return`:

```python
return "Hello Azure!"
```

Сохраните изменения и выйдите из редактора. Выполните команду `^S`, чтобы сохранить файл, и `^Q` — чтобы выйти.

Повторно разверните приложение с помощью команды [`az webapp up`](/cli/azure/webapp#az-webapp-up). Замените `<app-name>` на имя приложения и укажите расположение вместо `<location-name>` (с помощью одного из значений в выходных данных команды [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations)).

```bash
az webapp up -n <app-name> -l <location-name>
```

После завершения развертывания перейдите в окно браузера, открытое на шаге **перехода в приложение**, и обновите страницу.

![Запуск обновленного примера приложения Python в Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Управление новым приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>, чтобы управлять созданным приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего приложения Azure.

![Переход к приложению Python в Службе приложений на портале Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Отобразится страница обзора вашего приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

![Управление приложением Python на странице "Обзор" на портале Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

В меню слева доступно несколько страниц для настройки приложения. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Веб-приложение Python (Django) с PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Настройка приложения Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Руководство. Запуск приложения Python в настраиваемом контейнере](tutorial-custom-docker-image.md)

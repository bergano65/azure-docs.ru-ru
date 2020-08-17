---
title: Краткое руководство. Создание приложения Python
description: Начните работу со Службой приложений Azure, развернув первое приложение Python в контейнере Linux в Службе приложений.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 5463b23a4c19681515197f7d0cf880235ef6c0f2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121550"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Краткое руководство. Создание приложения Python в Службе приложений Azure в Linux

В этом кратком руководстве описывается процесс развертывания веб-приложения Python для [службы приложений на платформе Linux](overview.md#app-service-on-linux), высокомасштабируемой службы веб-размещения Azure с самостоятельной установкой исправлений. Вы используете локальный [интерфейс командной строки Azure (CLI)](/cli/azure/install-azure-cli) на компьютере Mac, Linux или Windows. Веб-приложение, которое вы настраиваете, использует бесплатный уровень Службы приложений, поэтому в процессе выполнения этого руководства затраты на ресурсы Azure не изменяются.

Если вы предпочитаете развертывать приложения с помощью интегрированной среды разработки, см. статью [Развертывание приложений Python в службе приложений из Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01).

## <a name="set-up-your-initial-environment"></a>Настройка начальной среды

Перед началом работы убедитесь, что у вас есть такие компоненты.

1. Подготовьте учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
1. Установите <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 или более поздней версии</a>.
1. Установите <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> версии 2.0.80 или более поздней для выполнения команд в любой оболочке для подготовки и настройки ресурсов Azure.

Откройте окно терминала и проверьте, что используется Python 3.6 или более поздней версии:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Убедитесь, что у вас есть Azure CLI версии 2.0.80 или выше.

```azurecli
az --version
```

Теперь войдите в Azure с помощью CLI.

```azurecli
az login
```

Эта команда открывает окно браузера для ввода учетных данных. После выполнения команда отображает выходные данные JSON с информацией о подписках.

Войдя, вы сможете выполнять в Azure CLI команды Azure для работы с ресурсами в подписке.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Клонирования репозитория

Выполните указанную ниже команду, чтобы клонировать репозиторий с примером. ([Установите Git](https://git-scm.com/downloads), если у вас его еще нет.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Затем перейдите в эту папку:

```terminal
cd python-docs-hello-world
```

Пример кода содержит файл *application.py*, который сообщает Службе приложений, что код содержит приложение Flask. Дополнительные сведения см. в разделе [Процесс запуска контейнера](configure-language-python.md#container-startup-process).

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Запуск примера

# <a name="bash"></a>[Bash](#tab/bash)

Для начала создайте виртуальную среду и установите необходимые зависимости.

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Затем задайте для переменной среды `FLASK_APP` модуль записи приложения и запустите сервер разработки Flask:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Для начала создайте виртуальную среду и установите необходимые зависимости.

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Затем задайте для переменной среды `FLASK_APP` модуль записи приложения и запустите сервер разработки Flask:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Для начала создайте виртуальную среду и установите необходимые зависимости.

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Затем задайте для переменной среды `FLASK_APP` модуль записи приложения и запустите сервер разработки Flask:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Откройте веб-браузер и перейдите к примеру приложения по адресу `http://localhost:5000/`. Приложение отображает сообщение **Hello World!** .

![Локальный запуск примера приложения Python](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

В окне терминала нажмите клавиши **CTRL**+**C**, чтобы выйти из сервера разработки Flask:

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Развертывание примера

Разверните код в локальной папке (*python-docs-hello-world*) с помощью команды `az webapp up`.

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Если команда `az` не распознана, убедитесь, что установлен Azure CLI, как описано в разделе [Настройка начальной среды](#set-up-your-initial-environment).
- Замените `<app_name>` именем, уникальным для всех регионов Azure (*допустимыми символами являются `a-z`, `0-9`и `-`* ). Рекомендуется использовать сочетание названия компании и идентификатора приложения.
- Аргумент `--sku F1` создает веб-приложение в ценовой категории "Бесплатный". Этот аргумент можно опустить, чтобы использовать более быструю ценовую категорию "Премиум" с почасовой оплатой.
- Вы также можете добавить аргумент `-l <location-name>`, где `<location_name>` — это регион Azure, например **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia** и т. д. Список допустимых регионов для учетной записи Azure можно получить, выполнив команду [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations).

Выполнение этой команды может занять несколько минут. Во время выполнения она предоставляет сообщения о создании группы ресурсов, плане службы приложений и приложении размещения, настройке ведения журнала и последующем выполнении развертывания ZIP-файла. Затем оно выдает сообщение You can launch the app at http://&lt;app-name&gt;.azurewebsites.net (Вы можете запустить приложение по адресу http://<app-name>.azurewebsites.net). Это URL-адрес приложения в Azure.

![Пример выходных данных команды az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Переход в приложение

Найдите развернутое приложение в веб-браузере по URL-адресу `http://<app-name>.azurewebsites.net`.

Пример кода Python запускает контейнер Linux в Службе приложений с помощью встроенного образа.

![Запуск примера приложения Python в Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Поздравляем!** Вы развернули свое приложение Python в Cлужбе приложений.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Повторное развертывание обновлений

Откройте в редакторе файл *application.py* и измените функцию `hello` следующим образом. Будет добавлена инструкция `print` для создания выходных данных журнала, которые понадобятся в следующем разделе. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Сохраните изменения и выйдите из редактора. 

Повторно разверните приложение с помощью команды `az webapp up`.

```azurecli
az webapp up
```

Эта команда использует значения, которые кэшируются локально в файле *.azure/config*, включая имя приложения, группу ресурсов и план службы приложений.

После завершения развертывания вернитесь в окно браузера и откройте `http://<app-name>.azurewebsites.net`. Обновите страницу, на которой должно отобразиться измененное сообщение:

![Запуск обновленного примера приложения Python в Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code предоставляет разнообразные расширения для Python и Службы приложений Azure, которые упрощают процесс развертывания веб-приложений Python в службе приложений. Дополнительные сведения см. в статье [Развертывание приложений Python в Службе приложений из Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Журналы потоковой передачи

Вы можете получить доступ к журналам консоли, созданным в приложении, и контейнеру, в котором он выполняется. Журналы содержат все выходные данные, созданные с помощью инструкций `print`.

Чтобы включить потоковую передачу журналов, выполните следующую команду:

```azurecli
az webapp log tail
```

Обновите приложение в браузере, чтобы создать журналы консоли, которые содержат сообщения с HTTP-запросами к приложению. Если выходные данные не отображаются немедленно, повторите попытку через 30 секунд.

Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить потоковую передачу журналов, нажмите клавиши **CTRL**+**C**.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Управление приложением Azure

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>, чтобы управлять созданным приложением. Найдите в поиске и выберите **Службы приложений**.

![Перейдите к разделу службы приложений на портале Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Выберите имя приложения Azure.

![Переход к приложению Python в Службе приложений на портале Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

При выборе приложения открывается страница **Обзор**, где можно выполнять основные задачи управления, такие как обзор, завершение, запуск, перезапуск и удаление.

![Управление приложением Python на странице "Обзор" на портале Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

В меню службы приложений слева доступно несколько страниц для настройки приложения.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Группа ресурсов имеет имя, такое как "appsvc_rg_Linux_CentralUS", в зависимости от расположения. Если вы используете номер SKU Службы приложений, отличающийся от бесплатного уровня F1, за эти ресурсы будет взиматься плата (см. сведения о [ценах на Службу приложений](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Если эти ресурсы вам не понадобятся в будущем, удалите группу ресурсов, выполнив следующие команды:

```azurecli
az group delete
```

Эта команда использует имя группы ресурсов, кэшированное в файле *.azure/config*.

Выполнение этой команды может занять около минуты.

[Возникли проблемы? Сообщите нам!](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Веб-приложение Python (Django) с PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Добавление возможности входа пользователя в веб-приложение Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Настройка приложения Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Руководство. Запуск приложения Python в настраиваемом контейнере](tutorial-custom-container.md)

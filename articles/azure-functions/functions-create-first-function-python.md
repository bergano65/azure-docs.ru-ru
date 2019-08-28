---
title: Создание функции, активируемой HTTP, в Azure
description: Узнайте, как создать первую функцию Python в Azure с помощью Azure Functions Core Tools и Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 5b90702f89af260a67b69bf96c2e079a45298723
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575448"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Создание функции, активируемой HTTP, в Azure

В этой статье показано, как использовать программы командной строки для создания проекта Python, выполняющегося в решении "Функции Azure". Создаваемая функция активируется с помощью HTTP-запросов. Наконец, вы опубликуете проект для выполнения в качестве [независимой от сервера функции](functions-scale.md#consumption-plan) в Azure.

Эта статья представляет собой первое из двух кратких руководств по Функциям Azure. Выполнив действия из этой статьи, вы [добавите выходную привязку очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md) в свою функцию.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы вам необходимо выполнить следующее:

+ установить [Python 3.6](https://www.python.org/downloads/);

+ установить [Azure Functions Core Tools](./functions-run-local.md#v2) версии 2.7.1575 или более поздней;

+ установить [Azure CLI](/cli/azure/install-azure-cli) версии 2.x или более поздней;

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Создание и активация виртуальной среды (необязательно)

Чтобы локально разрабатывать и тестировать функции Python, рекомендуется использовать среду Python 3.6. Для создания и активации виртуальной среды с именем `.venv` выполните следующие команды.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Командная строка PowerShell или Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Остальные команды выполняются в виртуальной среде.

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

Проект Функций является эквивалентом приложения-функции в Azure. Проект может содержать несколько функций, которые совместно используют те же локальные конфигурации и конфигурации размещения.

Выполните в виртуальной среде следующую команду, выбрав **Python** в качестве рабочей среды.

```console
func init MyFunctionProj
```

Будет создана папка с именем _MyFunctionProj_, которая содержит следующие три файла:

* `local.settings.json` используется для хранения параметров приложения и строк подключения при локальном выполнении. Этот файл не публикуется в Azure.
* `requirements.txt` содержит список пакетов, которые должны быть установлены при публикации в Azure.
* `host.json` содержит параметры глобальной конфигурации, влияющие на все функции в приложении-функции. Этот файл не публикуется в Azure.

Перейдите к новой папке MyFunctionProj:

```console
cd MyFunctionProj
```

## <a name="create-a-function"></a>Создание функции

Чтобы добавить функцию в проект, выполните следующую команду:

```console
func new
```

Выберите шаблон **HTTP-триггера**, введите `HttpTrigger` в качестве имени функции и нажмите клавишу ВВОД.

Будет создана вложенная папка _HttpTrigger_, которая содержит следующие файлы:

* **function.json**. Файл конфигурации, который определяет функцию, триггер и другие привязки. Проверьте этот файл и убедитесь, что значение `scriptFile` указывает на файл, содержащий функцию, а триггер вызова и привязки определены в массиве `bindings`.

  Каждой привязке требуется направление, тип и уникальное имя. В HTTP-триггере есть входная привязка типа [`httpTrigger`](functions-bindings-http-webhook.md#trigger) и выходная привязка типа [`http`](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**. Файл скрипта, который является функцией, активируемой HTTP-запросами. Просмотрите этот сценарий и убедитесь, что он содержит `main()` по умолчанию. Данные HTTP из триггера передаются в эту функцию с помощью именованного параметра привязки `req`. Определенный в файле function.json, `req` является экземпляром [класса azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Возвращаемый объект, определенный как `$return` в файле function.json, — это экземпляр [класса azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Следующая команда запускает приложение-функцию, которое выполняется локально с использованием той же среды выполнения Функций Azure, которая находится в Azure.

```bash
func host start
```

При запуске узел функций записывает примерно следующие выходные данные, усеченные для удобства чтения:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на запрос GET, возвращаемый локальной функцией.

![Локальное тестирование в браузере](./media/functions-create-first-function-python/function-test-local-browser.png)

Теперь, когда вы выполнили функцию локально, можно создать приложение-функцию и другие необходимые ресурсы в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Создание приложения-функции в Azure

Приложение-функция предоставляет среду для выполнения кода функции. Это позволяет группировать функции в логические единицы, чтобы упростить управление, развертывание и совместное использование ресурсов.

Выполните следующею команду, используя уникальное имя приложения-функции, в котором будут отображаться заполнитель `<APP_NAME>` и имя учетной записи хранения `<STORAGE_NAME>`. `<APP_NAME>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Приложения Windows и Linux не могут размещаться в одной группе ресурсов. Если у вас есть группа ресурсов `myResourceGroup` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.

Эта команда также подготавливает связанный экземпляр Application Insights в той же группе ресурсов, которую можно использовать для мониторинга и просмотра журналов.

Теперь вы готовы опубликовать локальный проект функций в приложение-функцию в Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Чтобы просматривать журналы для опубликованного приложения Python, которые ведутся практически в режиме реального времени, рекомендуется использовать [Live Metrics Stream для Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Дополнительная информация

Вы создали проект функций Python с функцией, активируемой HTTP-запросами, запустили ее на своем локальном компьютере и развернули в Azure. Теперь расширьте свою функцию путем...

> [!div class="nextstepaction"]
> [Добавления выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md)

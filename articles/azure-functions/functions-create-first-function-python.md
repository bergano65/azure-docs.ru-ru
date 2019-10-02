---
title: Создание функции, активируемой HTTP, в Azure
description: Узнайте, как создать первую функцию Python в Azure с помощью Azure Functions Core Tools и Azure CLI.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 03b8e12d63ba84b4e20d7263f1c2ecb8d912936d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203142"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Создание функции, активируемой HTTP, в Azure

В этой статье показано, как использовать программы командной строки для создания проекта Python, выполняющегося в решении "Функции Azure". Вы также создаете функцию, запускаемую по HTTP-запросу. Наконец, вы опубликуете проект для выполнения в качестве [независимой от сервера функции](functions-scale.md#consumption-plan) в Azure.

Эта статья представляет собой первое из двух кратких руководств Python по Функциям Azure. Выполнив действия из этого краткого руководства, вы сможете [добавить выходную привязку очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md) в свою функцию.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы вам следует:

+ установить [Python 3.6.x](https://www.python.org/downloads/);

+ установить [Azure Functions Core Tools](./functions-run-local.md#v2) версии 2.7.1575 или более поздней;

+ установить [Azure CLI](/cli/azure/install-azure-cli) версии 2.x или более поздней;

+ Активная подписка Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Создание и активация виртуальной среды (необязательно)

Для локальной разработки функций Python следует использовать среду Python 3.6.x. Для создания и активации виртуальной среды с именем `.venv` выполните следующие команды.

> [!NOTE]
> Если модуль venv не был установлен Python для вашего дистрибутива Linux, вы можете установить его с помощью следующей команды:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Командная строка PowerShell или Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

После активации виртуальной среды выполните в ней остальные команды. Чтобы выйти из виртуальной среды, выполните `deactivate`.

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

Проект "Функции" является эквивалентом приложения-функции в Azure. Проект может содержать несколько функций, которые совместно используют те же локальные конфигурации и конфигурации размещения.

1. В виртуальной среде выполните следующую команду:

    ```console
    func init MyFunctionProj
    ```

1. Выберите **python** в качестве рабочей среды выполнения.

    Команда создает папку _MyFunctionProj_. Она содержит следующие три файла:

    * *local.settings.json*: используется для хранения параметров приложения и строк подключения при локальном выполнении. Этот файл не публикуется в Azure.
    * *requirements.txt*: содержит список пакетов, которые система будет устанавливать при публикации в Azure.
    * *host.json*: содержит параметры глобальной конфигурации, влияющие на все функции в приложении-функции. Этот файл не публикуется в Azure.

1. Перейдите к новой папке *MyFunctionProj*:

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Создание функции

Добавьте функции в новый проект.

1. Чтобы добавить функцию в проект, выполните следующую команду:

    ```console
    func new
    ```

1. Используйте стрелку вниз, чтобы выбрать шаблон **Триггер HTTP**.

1. Когда появится запрос на ввод имени функции, введите *HttpTrigger* и нажмите клавишу ВВОД.

Эти команды создают вложенную папку с именем _HttpTrigger_. Она содержит следующие файлы:

* *function.json*. Файл конфигурации, который определяет функцию, триггер и другие привязки. Убедитесь, что значение `scriptFile` в этом файле указывает на файл, содержащий функцию, а триггер вызова и привязки определены в массиве `bindings`.

    Каждой привязке требуется направление, тип и уникальное имя. В HTTP-триггере есть входная привязка типа [`httpTrigger`](functions-bindings-http-webhook.md#trigger) и выходная привязка типа [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*. Файл скрипта, который является функцией, активируемой HTTP-запросами. Обратите внимание, что этот сценарий имеет значение по умолчанию `main()`. Данные HTTP из триггера передаются в функцию с помощью `req` с именем `binding parameter`. `req`, определенный в файле function.json, является экземпляром класса [azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Возвращаемый объект, определенный как `$return` в файле *function.json*, — это экземпляр класса [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Функция выполняется локально с помощью среды выполнения функций Azure.

1. Эта команда запускает приложение-функцию:

    ```console
    func host start
    ```

    При запуске узел функций Azure записывает примерно следующие выходные данные. Они усекаются для удобства чтения.

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
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера.

1. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан снимок экрана на запрос GET, возвращаемый в браузер локальной функцией.

    ![Проверьте локальное тестирование в браузере](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Нажмите клавиши CTRL + C, чтобы завершить работу приложения-функции.

Теперь, когда вы выполнили функцию локально, можно создать приложение-функцию и другие необходимые ресурсы в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Создание приложения-функции в Azure

Приложение-функция предоставляет среду для выполнения кода функции. Это позволяет группировать функции в логические единицы, чтобы упростить управление, развертывание и совместное использование ресурсов.

Выполните следующую команду: Замените `<APP_NAME>` уникальным именем приложения-функции. Замените `<STORAGE_NAME>` на имя учетной записи хранения. `<APP_NAME>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure.

> [!NOTE]
> Вы не можете разместить приложения Windows и Linux в одной группе ресурсов. Если у вас есть группа ресурсов `myResourceGroup` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Предыдущая команда также подготавливает связанный экземпляр Application Insights Azure в той же группе ресурсов. Этот экземпляр можно использовать для мониторинга приложения-функции и просмотра журналов.

Теперь вы готовы опубликовать локальный проект функций в приложение-функцию в Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

Создав в Azure приложение-функцию, вы можете развернуть код проекта в Azure с помощью команды Core Tools [func azure functionapp publish](functions-run-local.md#project-file-deployment). В этом примере следует заменить `<APP_NAME>` именем приложения.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Параметр `--build remote` позволяет удаленно выполнить сборку проекта Python в Azure из файлов в пакете развертывания. 

Появится выходные данные, аналогичные указанному ниже сообщению. Они усекаются для удобства чтения.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Вы можете скопировать значение `Invoke url` для `HttpTrigger` и использовать его для проверки функции в Azure. URL-адрес содержит значение строки запроса `code`, которое является ключом функции, что затрудняет вызов конечных точек триггера HTTP в Azure другими пользователями.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Чтобы просматривать журналы для опубликованного приложения Python, которые ведутся практически в режиме реального времени, используйте [Live Metrics Stream для Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Дополнительная информация

Вы создали проект функций Python с функцией, активируемой HTTP-запросами, запустили ее на своем локальном компьютере и развернули в Azure. Теперь расширьте свою функцию путем...

> [!div class="nextstepaction"]
> [Добавления выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md)

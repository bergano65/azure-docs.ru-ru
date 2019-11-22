---
title: Создание функции Python, активируемой HTTP, в Azure
description: Узнайте, как создать первую функцию Python в Azure с помощью Azure Functions Core Tools и Azure CLI.
author: ggailey777
ms.author: glenga
ms.date: 11/07/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 61465177c98a31a739946097ca615382175df3d4
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082771"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Краткое руководство. Создание функции Python, активируемой HTTP, в Azure

В этой статье показано, как использовать программы командной строки для создания проекта Python, выполняющегося в решении "Функции Azure". Вы также создаете функцию, запускаемую по HTTP-запросу. После запуска в локальной среде вы опубликуете проект для выполнения в качестве [независимой от сервера функции](functions-scale.md#consumption-plan) в Azure. 

Эта статья представляет собой первое из двух кратких руководств Python по Функциям Azure. Выполнив действия из этого краткого руководства, вы сможете [добавить выходную привязку очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md) в свою функцию.

Существует также версия этой статьи для [Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы вам следует:

+ Установить [Python 3.7.4](https://www.python.org/downloads/). Эта версия Python проверяется с помощью функций. Python версии 3.8 и более поздних пока не поддерживается.

+ Установить [Azure Functions Core Tools](./functions-run-local.md#v2) версии 2.7.1846 или более поздней.

+ Установить [Azure CLI](/cli/azure/install-azure-cli) версии 2.0.76 или более поздней.

+ Активная подписка Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Создание и активация виртуальной среды

Для локальной разработки функций Python следует использовать среду Python 3.7. Для создания и активации виртуальной среды с именем `.venv` выполните следующие команды.

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

Проект службы "Функции" может содержать несколько функций, которые совместно используют те же локальные конфигурации и конфигурации размещения.

В виртуальной среде выполните следующую команду:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

Команда `func init` создает папку _MyFunctionProj_. Проект Python в этой папке пока не содержит функций. Вы добавите их позже.

## <a name="create-a-function"></a>Создание функции

Чтобы добавить функцию в проект, выполните следующую команду:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Эта команда создает вложенную папку _HttpTrigger_, которая содержит следующие файлы:

* *function.json*. Файл конфигурации, который определяет функцию, триггер и другие привязки. Убедитесь, что значение `scriptFile` в этом файле указывает на файл, содержащий функцию, а триггер вызова и привязки определены в массиве `bindings`.

    Каждой привязке требуется направление, тип и уникальное имя. В HTTP-триггере есть входная привязка типа [`httpTrigger`](functions-bindings-http-webhook.md#trigger) и выходная привязка типа [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*. Файл скрипта, который является функцией, активируемой HTTP-запросами. Обратите внимание, что этот сценарий имеет значение по умолчанию `main()`. Данные HTTP из триггера передаются в функцию с помощью `req` с именем `binding parameter`. `req`, определенный в файле function.json, является экземпляром класса [azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Возвращаемый объект, определенный как `$return` в файле *function.json*, — это экземпляр класса [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md).

Теперь новую функцию можно выполнить на локальном компьютере.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Эта команда запускает приложение-функцию с помощью среды выполнения Функций Azure (func.exe):

```console
func host start
```

Вы должны увидеть следующие выходные данные:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Скопируйте URL-адрес функции `HttpTrigger` из этих выходных данных и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан снимок экрана на запрос GET, возвращаемый в браузер локальной функцией.

![Проверьте локальное тестирование в браузере](./media/functions-create-first-function-python/function-test-local-browser.png)

Нажмите клавиши CTRL+C, чтобы завершить выполнение приложения-функции.

Теперь, когда функция запущена локально, вы можете развернуть код функции в Azure.  
Перед развертыванием приложения необходимо создать некоторые ресурсы Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Создание приложения-функции в Azure

Приложение-функция предоставляет среду для выполнения кода функции. Это позволяет группировать функции в логические единицы, чтобы упростить управление, развертывание и совместное использование ресурсов. 

Выполните следующую команду: Замените `<APP_NAME>` уникальным именем приложения-функции. Замените `<STORAGE_NAME>` на имя учетной записи хранения. `<APP_NAME>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure.

> [!NOTE]
> Вы не можете разместить приложения Windows и Linux в одной группе ресурсов. Если у вас есть группа ресурсов `myResourceGroup` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Предыдущая команда создает приложение-функцию, в которой используется Python 3.7.4. Кроме того, эта команда подготавливает связанный экземпляр Application Insights Azure в той же группе ресурсов. Этот экземпляр можно использовать для мониторинга приложения-функции и просмотра журналов. 

Теперь вы готовы опубликовать локальный проект функций в приложение-функцию в Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

Создав в Azure приложение-функцию, вы можете развернуть код проекта в Azure с помощью команды Core Tools [func azure functionapp publish](functions-run-local.md#project-file-deployment). В этом примере следует заменить `<APP_NAME>` именем приложения.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Параметр `--build remote` позволяет удаленно выполнить сборку проекта Python в Azure из файлов в пакете развертывания (что рекомендуется). 

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

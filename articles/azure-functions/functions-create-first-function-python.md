---
title: Создание первой функции Python в Azure
description: Узнайте, как создать первую функцию Python в Azure с помощью Azure Functions Core Tools и Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 2c14cdf963d1d6c792e6101d8a2179a560dcf096
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731263"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Создание первой функции Python в Azure (предварительная версия)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

В этом кратком руководстве вы узнаете, как использовать Azure CLI для создания первого [беcсерверного](https://azure.com/serverless) приложения-функции на платформе Linux. Код функции создается локально, а затем развертывается в Azure с помощью [Azure Functions Core Tools](functions-run-local.md). Дополнительные сведения о рекомендациях по предварительной версии для выполнения приложения-функции на платформе Linux см. [в этой статье](https://aka.ms/funclinux).

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux.

## <a name="prerequisites"></a>Предварительные требования

Для локального создания и тестирования необходимо:

+ установить [Python 3.6](https://www.python.org/downloads/);

+ установить [Azure Functions Core Tools](functions-run-local.md#v2) версии 2.2.70 или более поздней (требуется пакет SDK для .NET Core 2.x).

Чтобы публиковать и запускать в Azure, необходимо:

+ установить [Azure CLI]( /cli/azure/install-azure-cli) версии 2.x или более поздней;

+ наличие активной подписки Azure.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Создание и активация виртуальной среды

Чтобы создать проект "Функции", необходимо работать в виртуальной среде Python 3.6. Для создания и активации виртуальной среды с именем `.env` выполните следующие команды.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

Теперь можно создать локальный проект "Функции". Этот каталог является эквивалентом приложения-функции в Azure. Оно может содержать несколько функций, которые совместно используют ту же локальную конфигурацию и конфигурацию размещения.

В окне терминала или командной строке выполните следующую команду.

```bash
func init MyFunctionProj
```

В качестве среды выполнения выберите **python**.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Следующие выходные данные будут примером.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Создается новая папка _MyFunctionProj_. Чтобы продолжить, измените каталог этой папки.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Создание функции

Чтобы создать функцию, выполните следующую команду:

```bash
func new
```

Выберите `HTTP Trigger` как шаблон и укажите **имя функции** `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Следующие выходные данные будут примером.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Создается подпапка _HttpTrigger_. В ней содержится файл `__init__.py`, который является основным файлом сценария, и файл `function.json`, который описывает используемые функцией триггер и привязки. Дополнительные сведения о модели программирования см. в статье [Azure Functions Python developer guide](functions-reference-python.md) (Руководство разработчика Python по Функциям Azure).

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Используйте следующую команду для локального запуска узла службы "Функции".

```bash
func host start
```

При запуске узла службы "Функции" выводится URL-адрес функций, активируемых по HTTP. (Обратите внимание, что все выходные данные были усечены для удобства чтения).

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Скопируйте URL-адрес функции из выходных данных и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

На следующем снимке экрана показан активируемый из браузера ответ функции.

![Тестировать](./media/functions-create-first-function-python/function-test-local-browser.png)

Теперь вы можете создать приложение-функцию и другие необходимые ресурсы для публикации в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Создание приложения-функции Linux в Azure

Приложение-функция предоставляет среду для выполнения кода функции. Это позволяет группировать функции в логические единицы, чтобы упростить управление, развертывание и совместное использование ресурсов. Создайте **приложение-функцию Python, выполняемое в Linux** с помощью команды [az functionapp create](/cli/azure/functionapp).

Выполните следующею команду, используя уникальное имя приложения-функции, в котором будут отображаться заполнитель `<app_name>` и имя учетной записи хранения `<storage_name>`. `<app_name>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Если у вас есть группа ресурсов с именем `myResourceGroup` с приложениями службы приложений не на платформе Linux, необходимо использовать другую группу ресурсов. Вы не можете разместить приложения Windows и Linux в одной группе ресурсов.  

После создания приложения-функции может отобразиться следующее сообщение:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Теперь вы готовы опубликовать локальный проект функций в приложение-функцию в Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Развертывание проекта приложения-функции в облако Azure

С помощью Azure Functions Core Tools выполните следующие команды. Замените `<app_name>` на имя приложения, созданного на предыдущем этапе.

```bash
func azure functionapp publish <app_name>
```

Появится примерно следующий результат (сокращен для удобства чтения).

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о разработке Функций Azure с помощью Python.

> [!div class="nextstepaction"]
> [Azure Functions Python developer guide](functions-reference-python.md) (Руководство разработчика Python по Функциям Azure)
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

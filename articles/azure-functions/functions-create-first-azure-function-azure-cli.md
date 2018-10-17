---
title: Создание первой функции с помощью Azure CLI
description: Узнайте, как создать свою первую Функцию Azure для бессерверного выполнения с помощью Azure CLI и основных инструментов службы "Функции Azure".
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451126"
---
# <a name="create-your-first-function-from-the-command-line"></a>Создание первой функции из командной строки

В этом кратком руководстве рассматривается создание первой функции из командной строки или терминала. Используйте Azure CLI для создания приложения-функции, которое является [независимой от сервера](https://azure.microsoft.com/overview/serverless-computing/) инфраструктурой, где размещается ваша функция. Проект кода функции создается из шаблона с помощью [основных инструментов службы "Функции Azure"](functions-run-local.md), которые также используются для развертывания проекта приложения-функции в Azure.

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

+ Установка [основных инструментов Azure версии 2.x](functions-run-local.md#v2).

+ Установка [Azure CLI]( /cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Создание локального проекта приложения-функции

Выполните следующую команду из командной строки, чтобы создать проект приложения-функции в папке `MyFunctionProj` текущего локального каталога. Также в `MyFunctionProj` создается репозиторий GitHub.

```bash
func init MyFunctionProj
```

При появлении запроса с помощью клавиш со стрелками выберите время выполнения рабочей роли из приведенных ниже параметров языка.

+ `dotnet`. Создает проект библиотеки классов .NET (CSPROJ-файл).
+ `node`. Создает проект JavaScript.

При выполнении команды вы увидите выходные данные примерно следующего содержания.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Создание функции

Следующая команда выполняет переход к новому проекту и создает функцию с именем `MyHtpTrigger`, активируемую HTTP.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

При выполнении команды вы увидите выходные данные примерно следующего содержания, которые являются функцией JavaScript.

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>Изменение функции

По умолчанию шаблон создает функцию, которая при составлении запросов требует ключ функции. Для упрощения тестирования функции в Azure необходимо обновить функцию, чтобы разрешить анонимный доступ. Способ внесения этого изменения зависит от языка проекта функции.

### <a name="c"></a>C\#

Откройте файл кода MyHttpTrigger.cs (новую функцию) и обновите атрибут **AuthorizationLevel** в определении функции до значения `anonymous` и сохраните изменения.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Откройте файл function.json для новой функции, откройте его в текстовом редакторе, обновите свойство **authLevel** в **bindings.httpTrigger** до `anonymous` и сохраните изменения.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Теперь функцию в Azure можно вызвать без необходимости указания ключа функции. Ключ функции никогда не требуется при локальном выполнении.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Следующая команда запускает приложение-функцию. Приложение выполняется с помощью той же среды выполнения функций Azure, что и в Azure.

```bash
func host start --build
```

Параметр `--build` необходим для компиляции проектов C#. Этот параметр не нужен для проектов JavaScript.

При запуске узел функций записывает примерно следующие выходные данные, усеченные для удобства чтения.

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

Скопируйте URL-адрес функции `HTTPTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на запрос GET, возвращаемый локальной функцией.

![Локальное тестирование в браузере](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Теперь, когда вы выполнили функцию локально, можно создать приложение-функцию и другие необходимые ресурсы в Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции без сервера. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Параметр _deployment-source-url_ — это пример репозитория в GitHub, который содержит функцию Hello World, активируемую с помощью HTTP.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Если задать параметр _consumption-plan-location_, приложение-функция размещено в плане размещения по мере использования. В этом бессерверном плане ресурсы добавляются динамически, когда этого требуют функции, и оплачивается только выполнение функций. Дополнительные сведения см. в статье [Выбор правильного плана обслуживания для Функций Azure](functions-scale.md).

После создания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Настройка приложения-функции

Основные инструменты версии 2.x создают проекты с помощью шаблонов для среды выполнения функций Azure 2.x. По этой причине необходимо убедиться, что среда выполнения версии 2.x используется в Azure. Задав значение `~2` для параметра приложения `FUNCTIONS_WORKER_RUNTIME`, можно закрепить приложение-функцию в последней версии 2.x. Задайте параметры приложения с помощью команды [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

В следующей команде Azure CLI <app_name> — это имя приложения-функции.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

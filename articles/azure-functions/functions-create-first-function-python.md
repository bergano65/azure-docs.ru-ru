---
title: Создание функции Python, активируемой HTTP, в Azure
description: Создание и развертывание в облаке бессерверного кода Python с использованием функций Azure.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 452c5aeab5d2a1092cb7d338d37e26a82d92396e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845494"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Краткое руководство. Создание функции Python, активируемой HTTP, в Azure

В этой статье используются средства командной строки для создания функции Python, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure.

Существует также версия этой статьи для [Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01).

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 или более поздней версии.
- Для его работы требуется [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 или более поздней версии. 
- [Python 3.7.4, 64-разрядная версия](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 совместима с Функциями Azure. Python 3.8 и более поздние версии пока не поддерживаются.)

### <a name="prerequisite-check"></a>Проверка предварительных условий

1. В окне терминала или командном окне запустите `func --version`, чтобы убедиться, что используется версия Azure Functions Core Tools 2.7.1846 или более поздняя.
1. Выполните команду `az --version`, чтобы проверить, является ли версия Azure CLI версией 2.0.76 или более поздней.
1. Выполните команду `az login`, чтобы войти в Azure и проверить активную подписку.
1. Выполните команду `python --version` (в ОС Linux и MacOS) или `py --version` (в ОС Windows), чтобы убедиться, что для Python возвращается версия 3.7 или более поздняя.

## <a name="create-and-activate-a-virtual-environment"></a>Создание и активация виртуальной среды

В подходящей папке выполните следующие команды, чтобы создать и активировать виртуальную среду с именем `.venv`. Обязательно используйте версию Python 3.7, которую поддерживают Функции Azure.


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Если пакет venv не установлен Python для вашего дистрибутива Linux, выполните следующую команду:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Все последующие команды будут выполняться в этой активированной виртуальной среде. (Чтобы выйти из виртуальной среды, выполните команду `deactivate`.)

## <a name="create-a-local-function-project"></a>Создание локального проекта службы "Функции"

В Функциях Azure проект функций представляет собой контейнер для одной или нескольких отдельных функций, каждая из которых реагирует на конкретный триггер. Все функции в проекте совместно используют те же локальные конфигурации и конфигурации размещения. В этом разделе вы создадите проект функций, содержащий одну функцию.

1. В виртуальной среде выполните команду `func init`, чтобы создать проект функций в папке с именем *LocalFunctionProj* с указанной средой выполнения:

    ```
    func init LocalFunctionProj --python
    ```
    
    Эта папка содержит различные файлы проекта, в том числе файлы конфигурации [local.settings.json](functions-run-local.md#local-settings-file) и [host.json](functions-host-json.md). Файл *local.settings.json* может содержать секреты, скачанные из Azure, поэтому файл по умолчанию исключен из системы управления версиями в *GITIGNORE*-файле.

    > [!TIP]
    > Так как проект функций привязан к определенной среде выполнения, все функции в проекте должны быть написаны на одном языке.

1. Перейдите в папку проекта:

    ```
    cd LocalFunctionProj
    ```
    
1. Добавьте функцию в проект с помощью следующей команды, где аргумент `--name` — уникальное имя функции, а аргумент `--template` указывает триггер функции. С помощью `func new` создайте вложенную папку с именем функции, которая содержит файл кода на выбранном для проекта языке и файл конфигурации *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Проверка содержимого файла (дополнительно)

При необходимости можно сразу перейти к [локальному запуску функции](#run-the-function-locally) и просмотреть содержимое файла позже.

### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* содержит функцию Python `main()`, которая активируется в соответствии с конфигурацией в *function.json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Для триггера HTTP функция получает данные запроса в переменной `req` как определено в файле *function.json*. `req` — это экземпляр [класса azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Возвращаемый объект, определенный как `$return` в файле *function.json*, — это экземпляр класса [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*function.json* — это файл конфигурации, который определяет входные и выходные данные для функции `bindings`, в том числе тип триггера. При необходимости можно изменить `scriptFile`, чтобы вызывать другой файл Python.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
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
}
```

Для каждой привязки требуется направление, тип и уникальное имя. В HTTP-триггере есть входная привязка типа [`httpTrigger`](functions-bindings-http-webhook.md#trigger) и выходная привязка типа [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Локальное выполнение функции

Активируйте функцию, запустив локальное хост-приложение среды выполнения Функций Azure в папке *LocalFunctionProj*:

```
func start
```

Выходные данные будут аналогичны следующим. (Если HttpExample не отображается, как показано ниже, вероятно, вы запустили узел в папке *HttpExample*. В этом случае нажмите клавиши **Ctrl**+**C**, чтобы отключить узел, а затем перейдите в родительскую папку *LocalFunctionProj* и снова запустите `func start`.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Скопируйте URL-адрес функции `HttpExample` из этих выходных данных в браузер и добавьте строку запроса `?name=<your-name>`, сформировав полный URL-адрес, например `http://localhost:7071/api/HttpExample?name=Functions`. В браузере должно отобразиться сообщение, например `Hello Functions`:

![Получившаяся функция выполняется локально в браузере](./media/functions-create-first-function-python/function-test-local-browser.png)

Терминал, в котором вы выполнили `func start`, также выводит данные журнала при выполнении запросов.

Когда все будет готово, нажмите клавиши **Ctrl**+**C**, чтобы отключить узел функций.

## <a name="create-supporting-azure-resources-for-your-function"></a>Создание вспомогательных ресурсов Azure для функции

Чтобы развернуть код функции в Azure, необходимо создать три ресурса:

- группу ресурсов — логический контейнер связанных ресурсов;
- учетную запись хранения Azure — для сохранения состояния и других сведений о проектах;
- приложение-функцию Azure — среду для выполнения кода функций. Оно сопоставляется с локальным проектом функций и позволяет группировать функции в логические единицы, чтобы упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

Для создания этих элементов используются команды интерфейса командной строки Azure. Каждая команда предоставляет выходные данные JSON после завершения.

1. Войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login), если вы еще не сделали этого:

    ```azurecli
    az login
    ```
    
1. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем `AzureFunctionsQuickstart-rg` в регионе `westeurope`. (Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам регионе. Для этого используйте команду `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Вы не можете разместить приложения Windows и Linux в одной группе ресурсов. Если у вас есть группа ресурсов `AzureFunctionsQuickstart-rg` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.
    
1. В группе ресурсов создайте учетную запись хранения общего назначения и регион с помощью команды [az storage account create](/cli/azure/storage/account#az-storage-account-create). В следующем примере замените `<storage_name>` подходящим глобально уникальным именем приложения. Имена должны содержать от трех до 24 символов и только в нижнем регистре. `Standard_LRS` обозначает типичную учетную запись общего назначения.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    В этом кратком руководстве с учетной записи хранения будет взиматься плата лишь в несколько центов США.
    
1. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create). В примере ниже замените `<storage_name>` именем учетной записи, использованной на предыдущем шаге, и замените `<app_name>` на глобально уникальное имя, подходящее вам. `<app_name>` также является доменом DNS по умолчанию для приложения-функции.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Эта команда создает приложение-функцию, формирующее указанную языковую среду выполнения, в рамках [плана использования Функций Azure](functions-scale.md#consumption-plan), который предоставляется бесплатно для объема, используемого здесь. Эта команда также подготавливает связанный экземпляр Application Insights Azure в той же группе ресурсов. Его можно использовать для мониторинга приложения-функции и просмотра журналов. Дополнительные сведения см. в разделе [Мониторинг функций Azure](functions-monitoring.md). Этот экземпляр не создает затраты, пока вы не активируете его.
    
## <a name="deploy-the-function-project-to-azure"></a>Развертывание проекта функций в Azure

Теперь, когда необходимые ресурсы готовы, вы можете развернуть проект локальных функций в приложении-функции в Azure с помощью команды [func azure functionapp publish](functions-run-local.md#project-file-deployment). В следующем примере замените `<app_name>` на имя своего приложения.

```
func azure functionapp publish <app_name>
```

Если отображается сообщение об ошибке Can't find app with name... (Не удалось найти приложение с именем...), подождите несколько секунд и повторите попытку. Возможно, в Azure не полностью инициализировано приложение после выполнения предыдущей команды `az functionapp create`.

Команда publish показывает результаты, аналогичные приведенным ниже (усечены для простоты):

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Вызов функции в Azure

Функция использует триггер HTTP, поэтому ее необходимо вызывать через HTTP-запрос по URL-адресу в браузере или с помощью такого средства, как cURL. В обоих экземплярах параметр URL-адреса `code` является уникальным ключом функции, который разрешает вызов с использованием конечной точкой функции.

# <a name="browsertabbrowser"></a>[Браузер](#tab/browser)

Скопируйте полный URL-адрес вызова **Invoke url**, показанный в выходных данных команды publish, в адресную строку браузера, добавив параметр запроса `&name=Azure`. В браузере должны отображаться выходные данные, аналогичные данным при локальном запуске функции.

![Выходные данные функции, выполняемой в Azure в браузере](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Запустите [cURL](https://curl.haxx.se/), используя **Invoke url** и добавив параметр `&name=Azure`. Результатом выполнения этой команды должен быть текст "Hello Azure".

![Выходные данные функции, выполненной в Azure в cURL](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Чтобы просматривать журналы для опубликованного приложения Python, которые ведутся практически в режиме реального времени, используйте [Live Metrics Stream для Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему шагу, [Добавление выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md), вам потребуется сохранить все ресурсы, чтобы использовать их в будущем.

В противном случае используйте следующую команду, чтобы удалить группу ресурсов и все содержащиеся в ней ресурсы и избежать дополнительных расходов.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-python.md)

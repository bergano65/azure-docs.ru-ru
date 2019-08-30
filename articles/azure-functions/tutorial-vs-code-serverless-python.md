---
title: Создание и развертывание функций Azure в Python с помощью Visual Studio Code
description: Использование расширения Visual Studio Code для функций Azure для создания бессерверных функций в Python и их развертывания в Azure.
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 4f5c10536992f51ac61815507a3869e521520299
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170707"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Развертывание Python в функциях Azure с помощью Visual Studio Code

В этом руководстве вы используете Visual Studio Code и расширение функций Azure, чтобы создать бессерверную конечную точку HTTP с помощью Python, а также добавить подключение (или "привязку") в хранилище. Функции Azure выполняют код в бессерверной среде без необходимости подготавливать виртуальную машину или публиковать веб-приложение. Расширение "функции Azure" для Visual Studio Code значительно упрощает процесс использования функций путем автоматической обработки множества проблем с конфигурацией.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Установка расширения "Функции Azure"
> * Создание функции, активируемой по HTTP
> * Локальная отладка
> * Синхронизация параметров приложения
> * Просмотр журналов потоковой передачи
> * Подключение к службе хранилища Azure

Если вы столкнулись с проблемами, изложенными в этом руководстве, мы будем рады узнать о деталях. Используйте кнопку "Отправить" в конце каждого раздела для отправки подробных отзывов.

## <a name="prerequisites"></a>Предварительные требования

- [Подписка Azure](#azure-subscription).
- [Visual Studio Code с расширением функций Azure](#visual-studio-code-python-and-the-azure-functions-extension) .
- [Azure functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Подписка Azure

Если у вас нет подписки Azure, [Зарегистрируйтесь сейчас](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) , чтобы получить бесплатную 30-дневную учетную запись с $200 в кредитах Azure, чтобы испытать любое сочетание служб.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python и расширение "функции Azure"

Установите следующее программное обеспечение:

- Python 3.6. x в соответствии с требованиями функций Azure. [3.6.8 Python](https://www.python.org/downloads/release/python-368/) — это последняя версия 3.6. x.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) , как описано в [руководстве по Visual Studio Code Python — предварительные требования](https://code.visualstudio.com/docs/python/python-tutorial).
- [Расширение функций Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Общие сведения см. в [репозитории GitHub vscode-азурефунктионс](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Следуйте инструкциям по [работе с](functions-run-local.md#v2)операционной системой Azure functions Core Tools. Сами средства написаны на .NET Core, а пакет основных средств лучше установить с помощью диспетчера пакетов Node. js, NPM, поэтому необходимо установить .NET Core и Node. js в настоящее время, даже для кода Python. Однако можно обойти требование .NET Core, используя "пакеты расширений", как описано в упомянутой выше документации. В любом случае необходимо установить эти компоненты только один раз, после чего Visual Studio Code автоматически предложит установить какие бы то ни было обновления.

### <a name="sign-in-to-azure"></a>Войдите в Azure

После установки расширения функций Войдите в свою учетную запись Azure, перейдя **в Azure: Обозреватель** функций, выберите **войти в Azure**и следуйте инструкциям на экране.

![Войдите в Azure с помощью Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

После входа убедитесь, что в строке состояния отображается учетная запись электронной почты подписки Azure:

![Строка состояния Visual Studio Code, показывающая учетную запись Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Имя, назначенное вашей подписке, также отображается в **Azure: Обозреватель** функций ("основной") на рисунке ниже:

![Visual Studio Code обозреватель службы приложений Azure, отображающий подписки](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Если возникла ошибка **"не удается найти подписку с именем [идентификатор подписки]"** , это может быть вызвано тем, что вы находится за прокси-сервером и не можете связаться с API Azure. Настройте `HTTP_PROXY` и`HTTPS_PROXY` переменные среды, используя сведения о прокси-сервере в терминале:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>проверка предварительных требований;

Чтобы убедиться, что все средства функций Azure установлены, откройте палитру команд Visual Studio Code (F1), выберите **терминал: Создайте новую интегрированную** команду терминала и после открытия терминала выполните команду: `func`

![Проверка предварительных требований для основных инструментов функций Azure](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Выходные данные, которые начинаются с логотипа функций Azure (нужно прокручивать выходные данные), указывают на наличие Azure Functions Core Tools.

`func` Если команда не распознается, убедитесь, что папка, в которую вы установили Azure functions Core Tools, включена в переменную среды PATH.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Создание функции

1. Код для функций Azure управляется в _проекте_функций, который создается в первую очередь перед созданием кода. В **Azure: Обозреватель** функций (открывается с помощью значка Azure в левой части экрана), выберите значок команды " **создать проект** " или откройте палитру команд (F1) и **выберите функции Azure: создать проект**.

    ![Кнопка "создать новый проект" в обозревателе функций](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. В следующих запросах:

    | Запрос | Значение | Описание | 
    | --- | --- | --- |
    | Укажите папку для проекта | Текущая открытая папка | Папка, в которой создается проект. Возможно, потребуется создать проект во вложенной папке. |
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | **Python** | Язык, используемый для функции, который определяет шаблон для кода. |
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | **триггером HTTP** | Функция, использующая триггер HTTP, выполняется всякий раз, когда в конечной точке функции выполняется HTTP-запрос. (Существует множество других триггеров для функций Azure. Дополнительные сведения см. в разделе [что можно делать с помощью функций?](functions-overview.md#what-can-i-do-with-functions).) |
    | Provide a function name (Укажите имя функции) | хттпексампле | Имя используется для вложенной папки, которая содержит код функции вместе с данными конфигурации, а также определяет имя конечной точки HTTP. Используйте "Хттпексампле" вместо принятия значения по умолчанию "HTTPTrigger", чтобы отличать саму функцию от триггера. |
    | Уровень авторизации | **Function** | Вызовы, выполненные в конечной точке функции, должны иметь [ключ функции](functions-bindings-http-webhook.md#authorization-keys). |
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | **Открыть в текущем окне** | Открывает проект в текущем окне Visual Studio Code. |

1. Через некоторое время появляется сообщение о том, что был создан новый проект. В **обозревателе**есть вложенная папка, созданная для функции. 

1. Если он еще не открыт, откройте  *\_ \_файл\_init\_. корр* , содержащий код функции по умолчанию:

    [![Результат создания проекта функций Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Когда Visual Studio Code сообщает, что при открытии  *\_ \_\_init\_.* **корректировки не выбран интерпретатор Python, откройте палитру команд (F1), выберите Python: Выберите** команду интерпретатор, а затем выберите виртуальную среду в локальной `.env` папке (которая была создана как часть проекта). Среда должна основываться на Python 3.6 x специально, как отмечалось ранее в разделе [Предварительные требования](#prerequisites).
    >
    > ![Выбор виртуальной среды, созданной с помощью проекта](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Проверка файлов кода

В созданной подпапке _хттпексампле_ функции содержатся три файла:  *\_ \_init\_\_. Корректировка* содержит код функции, *Function. JSON* описывает функцию в Azure. Функции, а *Sample. dat* — пример файла данных. При необходимости можно удалить *Sample. dat* , так как он существует только для того, чтобы показывать, что можно добавить другие файлы во вложенную папку.

Давайте рассмотрим сначала *Function. JSON* , а затем код в  *\_ \_\_init\_. корректировки*.

### <a name="functionjson"></a>function.json

Файл function. JSON предоставляет необходимые сведения о конфигурации для конечной точки функции Azure:

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

Свойство определяет файл запуска для кода, и этот код должен содержать функцию Python с именем `main`. `scriptFile` Вы можете разделить код на несколько файлов, пока указанный здесь файл содержит `main` функцию.

`bindings` Элемент содержит два объекта: один для описания входящих запросов, а другой — для описания ответа HTTP. Для входящих запросов (`"direction": "in"`) функция реагирует на запросы HTTP GET или POST и требует, чтобы была указана ключ функции. Ответ (`"direction": "out"`) — это HTTP-ответ, возвращающий любое значение, возвращаемое `main` функцией Python.

### <a name="__initpy__"></a>\_\_init.py\_\_

При создании новой функции функции Azure предоставляют код Python по умолчанию в  *\_ \_\_init\_. корректировки*:

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

Ниже приведены важные части кода.

- Необходимо импортировать `func` из `azure.functions`; импорт модуля ведения журнала является необязательным, но рекомендуется.
- Обязательная `main` функция Python `func.request` получает объект с именем `req`и возвращает значение типа `func.HttpResponse`. Дополнительные сведения о возможностях этих объектов см. в описании функции [Func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) и [Func. Ссылки HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) .
- `main` Затем тело обрабатывает запрос и создает ответ. В этом случае код ищет `name` параметр в URL-адресе. В таком случае он проверяет, содержит ли текст запроса JSON (с помощью `func.HttpRequest.get_json`) и что JSON `name` содержит значение (с помощью `get` метода объекта JSON, `get_json`возвращаемого).
- Если имя найдено, код возвращает строку "Hello" с добавленным именем; в противном случае возвращается сообщение об ошибке.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Локальная отладка

1. При создании проекта функций Visual Studio Code расширение также создает конфигурацию запуска в `.vscode/launch.json` , которая содержит одну конфигурацию с именем **attach to Python functions**. Такая конфигурация означает, что можно просто нажать клавишу F5 или использовать обозреватель отладки для запуска проекта:

    ![Обозреватель отладки, отображающий конфигурацию запуска функций](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. При запуске отладчика откроется окно с результатами работы функций Azure, включая сводку доступных конечных точек. URL-адрес может отличаться, если вы использовали имя, отличное от "Хттпексампле":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Нажмите **клавиши CTRL + щелчок** или **cmd + щелкните** URL-адрес в окне **вывода** Visual Studio Code, чтобы открыть браузер по этому адресу, или запустите браузер и вставьте тот же URL-адрес. В любом случае конечной точкой является `api/<function_name>`, в данном случае. `api/HttpExample` Однако, поскольку этот URL-адрес не включает параметр name, окно браузера должно просто показывать ", передать имя в строке запроса или в тексте запроса" в соответствии с этим путем в коде.

1. Теперь попробуйте добавить в использование параметр name, например `http://localhost:7071/api/HttpExample?name=VS%20Code`, и в окне браузера должно отобразиться сообщение «Hello Visual Studio Code!», в котором демонстрируется выполнение этого пути кода.

1. Чтобы передать значение имени в тексте запроса JSON, можно использовать такой инструмент, как фигурная скобка, с встроенным JSON:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Кроме того, создайте файл, например *Data. JSON* , который `{"name":"Visual Studio Code"}` содержит и используйте команду `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Чтобы выполнить отладку функции, установите точку останова в строке, `name = req.params.get('name')` которая считывает и снова выполняет запрос к URL-адресу. Отладчик Visual Studio Code должен останавливаться на этой строке, что позволяет проверять переменные и выполнять код по шагам. (Краткое пошаговое руководство по базовой отладке см. в разделе [Visual Studio Code учебник. Настройка и запуск отладчика](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).)

1. Если вы удовлетворены тщательной тестированием функции в локальной среде, закройте отладчик (с помощью команды меню **Отладка** > для**завершения отладки** или команду **Отключить** на панели инструментов Отладка).

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Развертывание в Функции Azure

В этих шагах вы используете расширение функций, чтобы создать приложение-функцию в Azure, а также другие необходимые ресурсы Azure. позволяющее группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Также требуется учетная запись хранения Azure для данных и [план размещения](functions-scale.md#hosting-plan-support). Все эти ресурсы организованы в одну группу ресурсов.

1. В области **Azure: Обозреватель** функций выберите команду **Deploy to приложение-функция** или откройте палитру команд (F1 **) и выберите функции Azure: Выполните команду "** развернуть в приложение-функция". Опять же, приложение-функция заключается в том, где выполняется проект Python в Azure.

    ![Команда "развернуть в приложение-функция"](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. При появлении запроса выберите **создать новый приложение-функция в Azure**и укажите уникальное имя в Azure (обычно с помощью личного имени или названия организации вместе с другими уникальными идентификаторами) можно использовать буквы, цифры и дефисы. Если вы ранее создали приложение-функция, его имя появится в списке параметров.

1. Расширение выполняет следующие действия, которые можно наблюдать Visual Studio Code всплывающих сообщениях и окне **вывода** (процесс занимает несколько минут):

    - Создайте группу ресурсов, используя заданное вами имя (удаление дефисов).
    - В этой группе ресурсов создайте учетную запись хранения, план размещения и приложение функции. По умолчанию создается [план потребления](functions-scale.md#consumption-plan) . Для выполнения функций в выделенном плане необходимо [включить публикацию с дополнительными параметрами создания](functions-develop-vs-code.md).
    - Разверните код в приложении функции.

    **Azure: В** обозревателе функций также отображается ход выполнения:

    ![Индикатор хода выполнения развертывания в Azure: Обозреватель функций](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. После завершения развертывания в расширении функций Azure отобразится сообщение с кнопками для трех дополнительных действий:

    ![Сообщение, указывающее на успешное развертывание с дополнительными действиями](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Сведения о потоковых журналах и **передачах параметров**см. в следующих разделах. **Выходные данные представления**см. в шаге 5 ниже.

1. После развертывания в окне **вывода** также отобразится общедоступная конечная точка в Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Используйте эту конечную точку для выполнения тех же тестов, которые вы сделали локально, используя параметры URL-адреса и (или) запросы с данными JSON в тексте запроса. Результаты общедоступной конечной точки должны соответствовать результатам проверенной ранее локальной конечной точки.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Журналы потоковой передачи

Поддержка потоковой передачи журналов в настоящее время находится на этапе разработки, как описано в [статье](https://github.com/microsoft/vscode-azurefunctions/issues/589) о Вы589 пуске функции Azure для расширения функций. При нажатии кнопки **потоковые журналы** в всплывающем окне сообщения о развертывании в конечном итоге будет подключаться выходные данные журнала в Azure для Visual Studio Code. Вы также сможете запустить и завершить поток журнала в обозревателе **функций Azure** , щелкнув правой кнопкой мыши проект функции и выбрав пункт **запустить потоковую передачу журналов** или **завершить журналы потоковой передачи**.

Однако в настоящее время эти команды еще не работают. Потоковая передача журнала доступна в браузере, выполнив следующую команду, заменив `<app_name>` на имя приложения-функции в Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Синхронизация локальных параметров с Azure

Кнопка **Отправить параметры** в всплывающем окне сообщения о развертывании применяет все изменения, внесенные в файл *Local. Settings. JSON* , в Azure. Вы также можете вызвать команду в обозревателе **функций Azure** , развернув узел проекта функции, щелкнув правой кнопкой мыши **Параметры приложения**и выбрав пункт **Отправить локальные параметры...** . Вы также можете использовать палитру команд для выбора **функций Azure: Команда "Отправить** локальные параметры".

При отправке параметров обновляются все существующие параметры и добавляются новые параметры, определенные в *Local. Settings. JSON*. При отправке не удаляются никакие параметры из Azure, которые не указаны в локальном файле. Чтобы удалить эти параметры, разверните узел **Параметры приложения** в обозревателе **функций Azure** , щелкните правой кнопкой мыши параметр и выберите команду **удалить параметр...** . Можно также изменить параметры непосредственно на портал Azure.

Чтобы применить изменения, выполняемые на портале, или с помощью **Azure Explorer** к *локальному файлу. Settings. JSON* , щелкните правой кнопкой мыши узел **Параметры приложения** и выберите команду **скачать удаленные параметры...** . Вы также можете использовать палитру команд для выбора **функций Azure: Команда загрузки удаленных** параметров.

## <a name="add-a-second-function"></a>Добавление второй функции

После первого развертывания можно внести изменения в код, например добавить дополнительные функции, и выполнить повторное развертывание в том же приложении функций.

1. В области **Azure: Обозреватель** функций, выберите команду **создать функцию** или используйте **функции Azure: Создание функции** из палитры команд. Укажите следующие сведения о функции:

    - Шаблон: Триггер HTTP
    - Имя: "Дигитсофпи"
    - Уровень авторизации: Anonymous

1. В Visual Studio Code Explorer — это вложенная папка с именем функции, которая снова содержит файлы с  *\_именами\_ \_\_init. корректировки*, *Function. JSON*и *Sample. dat*.

1. Замените содержимое файла  *\_ \_init.корректировки\_следующим кодом, который создает строку, содержащую значение PI, в число цифр, указанное в URL-адресе (в этом коде используется только параметр URL-адреса).\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Так как код поддерживает только HTTP GET, измените *Function. JSON* , `"methods"` чтобы коллекция содержала `"get"` только (то есть удалить `"post"`). Весь файл должен выглядеть следующим образом:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
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

1. Запустите отладчик, нажав клавишу F5 или выбрав команду > меню Отладка**начать отладку** . В окне **вывода** должны отобразиться обе конечные точки в проекте:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. В браузере или в фигурной скобке выполните запрос `http://localhost:7071/api/DigitsOfPi?digits=125` и просмотрите выходные данные. (Вы можете заметить, что алгоритм кода не вполне точен, но мы будем совершенствовать его.) По завершении закройте отладчик.

1. Повторно разверните код, используя **развертывание в приложение-функция** в **Azure: Обозреватель** функций. При появлении запроса выберите созданную ранее приложение-функция.

1. По завершении развертывания (займет несколько минут!) в окне **вывод** отображаются общедоступные конечные точки, с помощью которых можно повторить тесты.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Добавление привязки для записи сообщений в службу хранилища Azure

_Привязка_ позволяет подключать код функции к ресурсам, таким как служба хранилища Azure, без написания кода доступа к данным. Привязка определяется в файле *Function. JSON* и может представлять входные и выходные данные. Функция может использовать несколько входных и выходных привязок, но только один триггер. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этом разделе вы добавите привязку хранилища к функции Хттпексампле, созданной ранее в этом руководстве. Функция использует эту привязку для записи сообщений в хранилище с каждым запросом. В вопросе хранилища используется та же учетная запись хранения по умолчанию, которая используется приложением функции. Однако при планировании интенсивного использования хранилища следует подумать о создании отдельной учетной записи.

1. Синхронизируйте удаленные параметры проекта функций Azure в файле *Local. Settings. JSON* , открыв палитру команд и выбрав **функции Azure: Скачивание удаленных параметров**. Откройте *Local. Settings. JSON* и убедитесь, что он содержит значение для `AzureWebJobsStorage`. Это значение представляет собой строку подключения для учетной записи хранения.

1. В папке щелкните правой кнопкой мыши *функцию. JSON*и выберите **Добавить привязку:** `HttpExample`

    ![Добавить команду привязки в обозревателе Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. В следующих запросах в Visual Studio Code выберите или укажите следующие значения:

    | Запрос | Значение для предоставления |
    | --- | --- |
    | Задать направление привязки | out |
    | Выбор привязки с направлением | Хранилище очередей Azure |
    | Имя, используемое для распознавания этой привязки в коде | об |
    | Очередь, в которую будет отправлено сообщение | outqueue |
    | Выберите параметр из *Local. Settings. JSON* (запрос на подключение к хранилищу) | AzureWebJobsStorage |

1. После выбора параметров убедитесь, что в файл *Function. JSON* добавлена следующая привязка:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Теперь, когда привязка настроена, ее можно использовать в коде функции. Опять же, только что определенная Привязка отображается в коде в качестве аргумента `main` функции в  *\_ \_init\_\_. корректировки*. Например, можно `msg` изменить  *\_ \_файл\_init\_. корректировки* в хттпексампле в соответствии со следующим параметром, который показывает, как использовать аргумент для записи сообщения с меткой времени с именем, используемым в получения. В комментариях объясняются конкретные изменения:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
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
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Чтобы проверить эти изменения локально, запустите отладчик снова в Visual Studio Code, нажав клавишу F5 или выбрав команду > меню Отладка**начать отладку** . Как и прежде, в окне **вывода** должны отобразиться конечные точки в проекте.

1. В браузере перейдите по URL-адресу `http://localhost:7071/api/HttpExample?name=VS%20Code` , чтобы создать запрос к конечной точке хттпексампле, который также должен записывать сообщение в очередь.

1. Чтобы убедиться, что сообщение было записано в очередь "Queue" (в соответствии с именем в привязке), можно использовать один из трех методов:

    1. Войдите в [портал Azure](https://portal.azure.com)и перейдите к группе ресурсов, содержащей проект функций. В этой группе ресурсов перейдите в локальную учетную запись хранения для проекта, а затем перейдите в **очередь**. На этой странице перейдите в «очередь», в которой должны отображаться все сообщения, записанные в журнал.

    1. Просмотрите очередь с помощью Обозреватель службы хранилища Azure, который интегрируется с Visual Studio, как описано в статье [функции подключения к службе хранилища Azure с помощью Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), особенно в разделе [Проверка очереди вывода](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Используйте Azure CLI, чтобы запросить очередь хранилища, как описано в разделе [запрос очереди хранилища](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Чтобы выполнить тестирование в облаке, повторно разверните код, используя **развертывание в приложение-функция** в **Azure: Обозреватель** функций. При появлении запроса выберите созданную ранее приложение-функция. По завершении развертывания (займет несколько минут!) в окне **вывода** снова отобразятся общедоступные конечные точки, с помощью которых можно повторить тесты.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Очистка ресурсов

Созданная приложение-функция включает ресурсы, которые могут потребовать минимальных затрат (см. [цены на функции](https://azure.microsoft.com/pricing/details/functions/)). Чтобы очистить ресурсы, щелкните правой кнопкой мыши приложение-функция в **Azure: Обозреватель** функций и выберите **удалить приложение-функция**. Можно также посетить [портал Azure](https://portal.azure.com), выбрать **группы ресурсов** в левой области навигации, выбрать группу ресурсов, которая была создана в процессе работы с этим руководством, а затем использовать команду **Удалить группу ресурсов** .

## <a name="next-steps"></a>Следующие шаги

Поздравляем с завершением этого пошагового руководства по развертыванию кода Python в службе "функции Azure"! Теперь вы готовы к созданию множества дополнительных бессерверных функций.

Как отмечалось ранее, дополнительные сведения о расширении функций см. в репозитории GitHub [vscode-азурефунктионс](https://github.com/Microsoft/vscode-azurefunctions). Вы также можете ознакомиться с проблемами и вкладами.

Ознакомьтесь с [обзором функций Azure](functions-overview.md) , чтобы изучить различные триггеры, которые можно использовать.

Дополнительные сведения о службах Azure, которые можно использовать из Python, включая хранилище данных, а также AI и Машинное обучение Services, см. в [центре разработчиков Python для Azure](/azure/python/?view=azure-python).

Существуют также другие расширения Azure для Visual Studio Code, которые могут оказаться полезными. Просто выполните поиск по запросу "Azure" в обозревателе расширений:

![Расширения Azure для Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Некоторые популярные модули:

- [База данных Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Служба приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Средства интерфейса командной строки Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

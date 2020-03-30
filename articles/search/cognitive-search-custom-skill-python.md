---
title: Пример пользовательских навыков (Python)
titleSuffix: Azure Cognitive Search
description: Для разработчиков Python изучите инструменты и методы создания пользовательского навыка с помощью Функций Azure и Visual Studio. Пользовательские навыки содержат модели или логику, определяемые пользователем, которые можно добавить в конвейер индексирования, обогащенный ИИ, в Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210471"
---
# <a name="example-create-a-custom-skill-using-python"></a>Пример: Создайте пользовательский навык с помощью Python

В этом примере навыков Azure Cognitive Search вы узнаете, как создать пользовательский навык Web API с помощью Python и Visual Studio Code. В примере используется [функция Azure,](https://azure.microsoft.com/services/functions/) которая реализует [пользовательский интерфейс навыков.](cognitive-search-custom-skill-interface.md)

Пользовательский навык прост по дизайну (он совпется с двумя строками), так что вы можете сосредоточиться на инструментах и технологиях, используемых для разработки пользовательских навыков в Python. Как только вы преуспеете с простым навыком, вы можете разветвить с более сложными сценариями.

## <a name="prerequisites"></a>Предварительные требования

+ Просмотрите [пользовательский интерфейс навыков](cognitive-search-custom-skill-interface.md) для внедрения в интерфейс ввода/вывода, который должен реализовать пользовательский навык.

+ Настройка среды. Мы следовали [этому учебнику сквозной](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) настройке бессерверной функции Azure с использованием Visual Studio Code и расширений Python. Учебник ведет вас через установку следующих инструментов и компонентов: 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Расширение Python для визуального кода студии](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Основные инструменты Azure функции](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Расширение "Функции Azure" для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Создание функции Azure

В этом примере используется функция Azure для демонстрации концепции размещения веб-API, но возможны другие подходы. При условии соблюдения [требований к интерфейсу для когнитивного навыка](cognitive-search-custom-skill-interface.md) выбранный вами подход не имеет значения. Однако Функции Azure упрощают создание пользовательских навыков.

### <a name="create-a-function-app"></a>Создание приложения-функции

С помощью шаблона проекта Функции Azure в Visual Studio Code можно создать проект и затем опубликовать его в виде приложения-функции в Azure. Приложение-функция позволяет группировать функции в логические единицы для развертывания и совместного использования ресурсов, а также управления ими.

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд. В палитре команд найдите и щелкните `Azure Functions: Create new project...`.

1. Выберите расположение для рабочей области проекта и нажмите кнопку **Выбрать**.

    > [!NOTE]
    > Рассматриваемые в этой статье шаги выполняются вне рабочей области. По этой причине не выбирайте папку проекта, которая является частью рабочего пространства.

1. Выберите язык для проекта приложения функции. Для этого учебника выберите **Python**.
1. Выберите версию Python (версия 3.7.5 поддерживается Функциями Azure)
1. Выберите шаблон для первой функции проекта. Выберите **триггер HTTP** для создания функции срабатывания HTTP в новом приложении функции.
1. Предоставьте имя функции. В этом случае давайте использовать **Concatenator** 
1. Выберите **функцию** в качестве уровня авторизации. Это означает, что мы предоставим [функциональный ключ](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) для вызова http-тольной точки функции. 
1. Выберите, как вы хотели бы открыть свой проект. Для этого шага **выберите Добавить в рабочее пространство** для создания приложения функции в текущем рабочем пространстве.

Редактор Visual Studio Code создаст проект приложения-функции в новой рабочей области. Проект будет содержать файлы конфигурации [host.json](../azure-functions/functions-host-json.md) и [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), а также файлы проекта для указанного языка. 

Новая функция срабатывания HTTP также создается в папке **Concatenator** проекта приложения функции. Внутри него будет файл под\_\_названием "init__.py", с этим содержанием:

```py
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

Теперь давайте изменим этот код, чтобы следовать [пользовательскому интерфейсу навыков).](cognitive-search-custom-skill-interface.md) Измените код со следующим содержанием:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Метод **transform_value** выполняет операцию на одной записи. Вы можете изменить метод для удовлетворения ваших конкретных потребностей. Не забудьте сделать любую необходимую проверку входных данных и вернуть любые ошибки и предупреждения, полученные, если операция не может быть завершена для записи.

### <a name="debug-your-code-locally"></a>Отчерепное код локально

Visual Studio Code упрощает отладку кода. Нажмите 'F5' или перейдите в меню **Debug** и выберите **Start Debugging.**

Вы можете установить любые точки разрыва на коде, нажав 'F9' на линии интереса.

После начала отладки функция будет работать локально. Вы можете использовать такой инструмент, как Почтальон или Скрипач, чтобы выдать запрос localhost. Обратите внимание на расположение локальной конечной точки на окне Терминала. 

## <a name="publish-your-function"></a>Опубликовать свою функцию

Если вы удовлетворены поведением функции, вы можете опубликовать его.

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд. В командной палитре ищите и выберите **Развертывание для функции App...**. 

1. Выберите подписку Azure, где вы хотите развернуть приложение.

1. Выберите **и создайте новое функциональное приложение в Azure**

1. Введите глобально уникальное имя для вашего функционального приложения.

1. Выберите версию Python (Python 3.7.x работает для этой функции).

1. Выберите место для нового ресурса (например, West US 2).

На этом этапе в подписке Azure будут созданы необходимые ресурсы для размещения новой функции Azure в Azure. Дождитесь завершения развертывания. В окне вывода будет отображаться состояние процесса развертывания.

1. На [портале Azure](https://portal.azure.com)перейдите на **все ресурсы** и ищите функцию, опубликованную им под названием. Если вы назвали его **Concatenator,** выберите ресурс.

1. Нажмите кнопку **</> Получить URL-функцию.** Это позволит вам скопировать URL для вызова функции.

## <a name="test-the-function-in-azure"></a>Тестирование функции в Azure

Теперь, когда у вас есть ключ узла по умолчанию, протестируйте функцию следующим образом:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Текст запроса
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Этот пример должен дать тот же результат, который вы видели ранее при запуске функции в локальной среде.

## <a name="connect-to-your-pipeline"></a>Подключение к конвейеру

Теперь, когда у вас есть новый пользовательский навык, можно добавить его в ваш набор навыков. Приведенный ниже пример показывает, как вызвать навык, чтобы свести название и автора документа в единое поле, которое мы называем merged_title_author. Замените `[your-function-url-here]` URL-адрес новой функции Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия
Поздравляем! Вы создали свой первый пользовательский навык. Теперь вы можете использовать ту же схему для добавления пользовательских функций. Нажмите на следующие ссылки, чтобы узнать больше.

+ [Силовые навыки: хранилище пользовательских навыков](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Добавьте пользовательский навык в конвейер обогащения ИИ](cognitive-search-custom-skill-interface.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Сопоставление обогащенных полей](cognitive-search-output-field-mapping.md)

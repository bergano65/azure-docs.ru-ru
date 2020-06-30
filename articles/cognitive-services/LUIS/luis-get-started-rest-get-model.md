---
title: Краткое руководство. Получение модели с помощью интерфейсов REST API — LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства по REST API вы узнаете, как с помощью cURL добавлять речевые фрагменты и обучать модель.
services: cognitive-services
author: roy-har
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: v-royha
ms.openlocfilehash: 00bc71f76b15455dd8e4f8242c5379e9e97a53f2
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119554"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Краткое руководство. Изменение модели с помощью cURL и REST

В этом кратком руководстве показано, как добавить примеры речевых фрагментов в приложение Pizza и обучить его. Примерами высказываний называют фразы пользователя на обычном языке, сопоставленные с тем или иным намерением. Предоставляя фразы для настроенных намерений, вы сообщаете LUIS ожидаемые варианты сообщений пользователя для каждого намерения.

В рамках этого руководства вы последовательно выполните три вызова REST.

- Сначала вы отправите пакет примеров речевых фрагментов, которые будут использоваться для обучения модели приложения Pizza, используя вызов [добавления меток в пакет REST](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09).
- Затем вы начнете сеанс обучения для приложения Pizza с помощью вызова [версии приложения для обучения REST](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45).
- Наконец, вы получите состояние сеанса обучения для приложения Pizza с помощью вызова [состояния обучения для получения версии REST](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46).

[Справочная документация](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись [LUIS](https://www.luis.ai).
* Текстовый редактор, например [Visual Studio Code](https://code.visualstudio.com/).
* Программа командной строки cURL. Эта программа уже установлена в macOS, большинстве дистрибутивов Linux и Windows 10 сборки 1803 и более поздних версий.

    Если вам нужно установить cURL, вы можете скачать эту программу на [странице загрузки cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Создание приложения Pizza

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Создание файла JSON для обучения приложения Pizza

Чтобы создать файл JSON с тремя примерами речевых фрагментов, сохраните следующие данные JSON в файл с именем `ExampleUtterances.JSON`:

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Примеры речевых фрагментов JSON имеют определенный формат.

Поле `text` содержит текст примера высказывания. Поле `intentName` должно соответствовать имени существующего намерения в приложении LUIS. Поле `entityLabels` является обязательным. Чтобы не помечать все сущности, укажите пустой массив.

Если массив entityLabels не пуст, `startCharIndex` и `endCharIndex` должны пометить сущность, указанную в поле `entityName`. Значения индекса начинаются с нуля. Если вы начинаете или завершаете метку пробелом в тексте, вызов API для добавления фразы завершается ошибкой.

## <a name="add-example-utterances"></a>Добавление примеров высказываний

1. Чтобы отправить пакет примеров речевых фрагментов, скопируйте эту команду в текстовый редактор:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Замените значения, начинающиеся с `***YOUR-`, собственными значениями.

    |Сведения|Назначение|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| Конечная точка URL-адреса для разработки. Например, https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/. Имя ресурса задается при создании ресурса.|
    |`***YOUR-APP-ID***`| Идентификатор приложения LUIS. |
    |`***YOUR-APP-VERSION***`| Версия приложения LUIS. Для приложения Pizza номером версии является 0.1 без кавычек.|
    |`***YOUR-AUTHORING-KEY***`|Ключ для разработки (32 символа).|

    Назначенные ключи и ресурсы отображаются на портале LUIS в разделе "Управление" на странице **ресурсов Azure**. Идентификатор приложения доступен в том же разделе "Управление" на странице **Параметры приложения**.

1. Запустите командную строку (Windows) или терминал (macOS и Linux) и перейдите в тот же каталог, в котором вы сохранили файл `ExampleUtterances.JSON`.

1. Скопируйте команду cURL из редактора и вставьте ее в командную строку (Windows) или терминал (macOS и Linux). Нажмите клавишу "Ввод", чтобы выполнить команду.

    Появится следующий ответ:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Ниже приведены выходные данные, отформатированные для удобочитаемости.

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Обучение модели приложения Pizza

1. Чтобы начать сеанс обучения для приложения Pizza, скопируйте эту команду в текстовый редактор:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Как и раньше, замените значения, начинающиеся с `***YOUR-`, собственными значениями.

1. Скопируйте команду cURL из редактора и вставьте ее в командную строку (Windows) или терминал (macOS и Linux). Нажмите клавишу "Ввод", чтобы выполнить команду.

    Появится следующий ответ:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Ниже приведены выходные данные, отформатированные для удобочитаемости.

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Получение состояния обучения

1. Чтобы получить состояние для сеанса обучения, скопируйте эту команду в текстовый редактор:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Как и раньше, замените значения, начинающиеся с `***YOUR-`, собственными значениями.

1. Скопируйте команду cURL из редактора и вставьте ее в командную строку (Windows) или терминал (macOS и Linux). Нажмите клавишу "Ввод", чтобы выполнить команду.

    Появится следующий ответ:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Ниже приведены выходные данные, отформатированные для удобочитаемости.

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл `ExampleUtterances.JSON` из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации для приложения](luis-concept-best-practices.md)

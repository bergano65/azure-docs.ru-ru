---
title: Форма Распознаватель пользовательских навыков (C )
titleSuffix: Azure Cognitive Search
description: Узнайте, как создать пользовательский навык распознавания форм с помощью C-и Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274579"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Пример: Создайте пользовательский навык распознавания форм

В этом примере навыков Azure Cognitive Search вы узнаете, как создать пользовательский навык распознавания форм с помощью C и Visual Studio. Распознавание формы анализирует документы и извлекает пары ключей/значений и данные таблицы. Обернув форму Распознавателя в [пользовательский интерфейс навыков,](cognitive-search-custom-skill-interface.md)вы можете добавить эту возможность в качестве шага в сквозной конвейер обогащения. Затем конвейер может загрузить документы и сделать другие преобразования.

## <a name="prerequisites"></a>Предварительные требования

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (любой выпуск).
- По крайней мере пять форм одного и того же типа. Вы можете использовать выборочные данные, предоставленные в этом руководстве.

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Обучение модели

Перед использованием этого навыка необходимо обучить модель распознавания форм с помощью входных форм. Следуйте [cURL quickstart,](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) чтобы узнать, как обучать модели. Вы можете использовать образец формы, представленные в том, что quickstart, или вы можете использовать свои собственные данные. Как только модель будет обучена, скопируйте значение ее идентификатора в безопасное место.

## <a name="set-up-the-custom-skill"></a>Настройка пользовательского навыка

В этом учебнике используется проект [AnalyseForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) в репозитории [Навыков поиска Azure.](https://github.com/Azure-Samples/azure-search-power-skills) Клонируйте этот репозиторий для локальной машины и перейдите на **Vision/AnalyseForm/,** чтобы получить доступ к проекту. Затем откройте _AnalyzeForm.csproj_ в Визуальной студии. Этот проект создает ресурс Azure Function, который выполняет [пользовательский интерфейс навыков](cognitive-search-custom-skill-interface.md) и может быть использован для обогащения Azure Cognitive Search. Он принимает документы формы в качестве входных данных и выводит (как текст) пары ключей/значений, которые вы указываете.

Во-первых, добавьте переменные среды уровня проекта. Найдите проект **AnalyseForm** на левой панели, нажмите на него вправо и выберите **Свойства.** В окне **свойств свойств** нажмите на вкладку **Debug,** а затем найдите поле **переменных среды.** Нажмите **Добавить,** чтобы добавить следующие переменные:
* `FORMS_RECOGNIZER_ENDPOINT_URL`значение, установленное на URL-адресе конечных точек.
* `FORMS_RECOGNIZER_API_KEY`значение, установленное на ключе подписки.
* `FORMS_RECOGNIZER_MODEL_ID`значение, установленное в идентификаторе модели, который вы обучили.
* `FORMS_RECOGNIZER_RETRY_DELAY`значение, установленное до 1000. Это значение — время в миллисекундах, которое программа будет ждать перед повторной попыткой запроса.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`значение, установленное до 100. Это значение — это количество раз, когда программа будет загонить службу при попытке получить успешный ответ.

Затем откройте _AnalyzeForm.cs_ `fieldMappings` и найдите переменную, которая ссылается на файл *field-mappings.json.* Этот файл (и переменная, которая ссылается на него) определяет список ключей, которые вы хотите извлечь из формы, и пользовательскую метку для каждого ключа. Например, значение `{ "Address:", "address" }, { "Invoice For:", "recipient" }` средств, скрипт сохранит только значения для `Address:` `Invoice For:` обнаруженных и полей, и `"address"` `"recipient"`будет маркировать эти значения с и, соответственно.

Наконец, `contentType` обратите внимание на переменную. Этот скрипт запускает заданную модель распознавания форм на удаленных документах, на которые ссылается URL, так что тип содержимого `application/json`. Если вы хотите проанализировать локальные файлы, включив их потоки байт в запросы HTTP, вам нужно будет изменить `contentType` соответствующий тип [MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) для вашего файла.

## <a name="test-the-function-from-visual-studio"></a>Тестирование функции из Visual Studio

После того, как вы отредактировали проект, сохраните его и установите проект **AnalyzeForm** в качестве стартап-проекта в Visual Studio (если он еще не установлен). Затем нажмите **F5,** чтобы запустить функцию в локальной среде. Используйте службу REST, как [Почтальон,](https://www.postman.com/) чтобы вызвать функцию.

### <a name="http-request"></a>HTTP-запрос

Вы сделаете следующий запрос на вызов функции.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Тело запроса

Начните с шаблона тела запроса ниже.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Здесь вам нужно будет предоставить URL-адрес формы, которая имеет тот же тип, что и формы, с которыми вы обучались. Для тестирования можно использовать одну из учебных форм. Если вы следили за quickstart cURL, ваши формы будут размещены в учетной записи хранения blob Azure. Откройте Explorer хранения Azure, найдите файл формы, нажмите его правой кнопкой мыши и выберите **Получить общую подпись доступа.** Следующее окно диалога предоставит URL и токен SAS. Введите эти `"formUrl"` строки в `"formSasToken"` поле тела запроса, соответственно.

> [!div class="mx-imgBorder"]
> ![Исследователь хранения Azure; выбирается pdf-документ](media/cognitive-search-skill-form/form-sas.png)

Если вы хотите проанализировать удаленный документ, которого нет в хранилище Azure blob, вставьте его URL в `"formUrl"` поле и оставьте `"formSasToken"` поле пустым.

> [!NOTE]
> Когда навык интегрирован в набор навыков, URL и токен будут предоставлены Cognitive Search.

### <a name="response"></a>Ответ

Вы должны увидеть отклик, аналогичный следующему примеру:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Публикация функции в Azure

Если вы удовлетворены поведением функции, вы можете опубликовать его.

1. В **Solution Explorer** в Visual Studio, право йгните проекта и выберите **Опубликовать**. Выберите **Создать новую** > **публикацию**.
1. Если вы еще не подключили Visual Studio к учетной записи Azure, выберите **Добавить учетную запись...**
1. Следуйте инструкциям на экране. Укажите уникальное имя для службы приложений, подписку Azure, группу ресурсов, план хостинга и учетную запись хранения, которая вы хотите использовать. Вы можете создать новую группу ресурсов, новый план хостинга и новую учетную запись хранения, если у вас их еще нет. Когда вы закончите, выберите **Создать**.
1. После завершения развертывания обратите внимание на URL-адрес сайта. Этот URL-адрес — адрес приложения функции в Azure. Сохраните его во временном месте.
1. На [портале Azure](https://portal.azure.com)перейдите к группе `AnalyzeForm` ресурсов и ищите опубликованную функцию. В разделе **Управление** должны отображаться ключи узла. Копируйте ключ узла *по умолчанию* и сохраните его во временном месте.

## <a name="connect-to-your-pipeline"></a>Подключение к конвейеру

Чтобы использовать этот навык в конвейере Cognitive Search, вам нужно добавить определение навыков в набор навыков. Следующий блок JSON представляет собой определение навыков образца (следует обновить входные данные и выводы, чтобы отразить ваш конкретный сценарий и среду набора навыков). Замените `AzureFunctionEndpointUrl` URL-адрес функции и замените `AzureFunctionDefaultHostKey` ключом хоста.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали пользовательский навык из службы распознавания форм Azure. Чтобы узнать больше о пользовательских навыков, см. 

* [Навыки поиска Azure: хранилище пользовательских навыков](https://github.com/*zure-Samples/azure-search-power-skills)
* [Добавьте пользовательский навык в конвейер обогащения ИИ](cognitive-search-custom-skill-interface.md)
* [Определение набора навыков](cognitive-search-defining-skillset.md)
* [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Карта обогащенных полей](cognitive-search-output-field-mapping.md)

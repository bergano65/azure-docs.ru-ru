---
title: 'Пример: создание пользовательского навыка в конвейере когнитивного поиска в службе "Поиск Azure" | Документация Майкрософт'
description: Демонстрация использования API перевода текста в пользовательском навыке, сопоставленном с конвейером индексирования когнитивного поиска в Поиске Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: efa85491f4b183a044ec5d9e5e6e3d11eebedbe3
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428438"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Пример: создание пользовательского навыка с помощью API перевода текста

В этом примере Узнайте, как создать веб-API пользовательских навыков. Этот навык будет принимать текст на любом языке и транслирует его на английский. Этот пример использует [Функцию Azure](https://azure.microsoft.com/services/functions/) для заключения [API перевода текста](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) в оболочку, чтобы он реализовывал интерфейс пользовательского навыка.

## <a name="prerequisites"></a>Технические условия

+ Узнайте о [навыков пользовательский интерфейс](cognitive-search-custom-skill-interface.md) статью, если вы не знакомы с интерфейс ввода вывода, который должен реализовывать пользовательского опыта.

+ [Зарегистрируйтесь для доступа к API перевода текстов](../cognitive-services/translator/translator-text-how-to-signup.md) и получите ключ API для его использования.

+ Установка [Visual Studio 2019](https://www.visualstudio.com/vs/) или более поздней версии, включая рабочую нагрузку разработки Azure.

## <a name="create-an-azure-function"></a>Создание функции Azure

Несмотря на то, что в этом примере использует функцию Azure для размещения веб-API, не требуется.  При условии соблюдения [требований к интерфейсу для когнитивного навыка](cognitive-search-custom-skill-interface.md) выбранный вами подход не имеет значения. Однако Функции Azure упрощают создание пользовательских навыков.

### <a name="create-a-function-app"></a>Создание приложения-функции

1. В меню "Файл" Visual Studio выберите **Создать** > **Проект**.

1. В диалоговом окне "Новый проект" выберите **Установленные**, разверните узел **Visual C#**  > **Облако**, выберите **Функции Azure**, укажите имя проекта и нажмите кнопку **ОК**. Имя приложения-функции должно быть допустимым в качестве пространства имен C#, поэтому не используйте символы подчеркивания, дефисы и другие символы, не являющиеся буквенно-цифровыми.

1. Выберите **функции Azure v2 (.NET Core)** . Это также можно сделать с помощью версии 1, но код, написанный ниже, основан на шаблоне версии 2.

1. Выберите тип **Триггер HTTP**.

1. Для учетной записи хранения можно выбрать **Нет**, так как для этой функции никакое хранилище не требуется.

1. Нажмите кнопку **ОК**, чтобы создать проект функции и функцию, активируемую с помощью HTTP.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Изменение кода для вызова службы Translate Cognitive Service

Visual Studio создает проект и класс в нем, содержащий стандартный код для выбранного типа функции. Атрибут метода *FunctionName* задает имя функции. Атрибут *HttpTrigger* указывает, что функция вызывается HTTP-запросом.

Замените содержимое файла *Function1.cs* следующим кодом:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Обязательно введите собственное значение *ключа* в методе *TranslateText*, используя ключ, полученный при регистрации для API перевода текста.

Этот пример является простым обогатителем, который работает только для одной записи за раз. Этот факт становится важным позже — при настройке размера пакета для набора навыков.

## <a name="test-the-function-from-visual-studio"></a>Тестирование функции из Visual Studio

Нажмите клавишу **F5** для запуска программы и тестирования поведения функции. В этом случае мы используем функцию ниже для перевода текста на испанском языке, на английском языке. Используйте Postman или Fiddler для выполнения вызова, как показано ниже:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Текст запроса
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Ответ
Вы должны увидеть отклик, аналогичный следующему примеру:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Публикация функции в Azure

Когда вы будете удовлетворены поведения этой функции, его можно опубликовать.

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**. Выберите **Создать** > **Опубликовать**.

1. Если вы еще не подключили Visual Studio к учетной записи Azure, выберите **Добавить учетную запись...**

1. Следуйте инструкциям на экране. Вам будет предложено указать учетную запись Azure, группу ресурсов, план размещения и учетной записи хранения, которую вы хотите использовать. При отсутствии группы ресурсов, плана размещения и учетной записи хранения их можно создать. По завершении выберите **Создать**.

1. После завершения развертывания Обратите внимание, что URL-адрес сайта. Это адрес приложения-функции в Azure. 

1. На [портале Azure](https://portal.azure.com) перейдите в группу ресурсов и найдите опубликованную функцию перевода. В разделе **Управление** должны отображаться ключи узла. Выберите значок **копирования** для ключа узла *по умолчанию*.  

## <a name="test-the-function-in-azure"></a>Тестирование функции в Azure

Теперь, когда у вас есть ключ узла по умолчанию, протестируйте функцию следующим образом:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Текст запроса
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

В этом примере следует производить тот же результат тот, который вы видели ранее, при выполнении функции в локальной среде.

## <a name="connect-to-your-pipeline"></a>Подключение к конвейеру
Теперь, когда у вас есть новый пользовательский навык, можно добавить его в ваш набор навыков. Приведенный ниже пример показывает, как вызвать навык. Так как навык не обрабатывает пакеты, добавьте инструкцию, ограничивающую максимальный размер пакета всего лишь значением ```1```, чтобы отправлять документы по одному.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия
Поздравляем! Вы создали вашей первой дополнительные пользовательские. Теперь вы можете использовать ту же схему для добавления пользовательских функций. 

+ [Добавление пользовательского навыка в конвейер когнитивного поиска](cognitive-search-custom-skill-interface.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Сопоставление обогащенных полей](cognitive-search-output-field-mapping.md)

---
title: 'Краткое руководство: обновление базы знаний с использованием REST, C# — QnA Maker'
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается обновление примера базы знаний QnA Maker программным способом с использованием REST. Определение JSON, которое используется для обновления базы знаний, позволяет добавлять, изменять или удалять пары вопросов и ответов.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 463fb4bc1cfd6ba7a7fc4df2fcde53bda7767488
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648239"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-c"></a>Краткое руководство: обновление базы знаний в QnA Maker с использованием C#

В этом кратком руководстве описано, как программным способом обновить имеющуюся базу знаний QnA Maker.  Этот код JSON позволяет обновить базу знаний путем добавления, изменения или удаления источников данных.

Этот API является эквивалентом изменения и нажатия кнопки **Save and train** (Сохранение и обучение) на портале QnA Maker.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Обновление.](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API. 
* [Получение сведений об операции](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails).

## <a name="prerequisites"></a>Предварительные требования

* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели.
* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса kbid, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Если у вас еще нет базы знаний, создайте пример для этого краткого руководства по инструкциям из [этой статьи](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Создание проекта базы знаний

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Добавление необходимых зависимостей

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Добавление необходимых констант

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-knowledge-base-id"></a>Добавление идентификатора базы знаний

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-the-kb-update-definition"></a>Добавление определения обновления базы знаний

После констант добавьте следующее определение обновления базы знаний. Это определение содержит три раздела:

* добавление
* обновить
* удалить

Каждый раздел может использоваться в одном запросе к API. 

```csharp
static string new_kb = @"
{
    'add': {
        'qnaList': [
            {
            'id': 1,
            'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
            'source': 'Custom Editorial',
            'questions': [
                'How can I change the default message from QnA Maker?'
            ],
            'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'QnA Maker FAQ from quickstart - updated'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Добавление вспомогательных функций и структур

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-patch-request-to-update-kb"></a>Добавление запроса PATCH для обновления базы знаний

Следующий код отправляет HTTPS-запрос к API службы QnA Maker, чтобы обновить группы вопросов и ответов базы знаний, и получает ответ:

```csharp
async static Task<Response> PatchUpdateKB(string kb, string new_kb)
{
    string uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = new HttpMethod("PATCH");
        request.RequestUri = new Uri(uri);

        request.Content = new StringContent(new_kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Добавление запроса GET для определения состояния создания

Обновление базы знаний добавляет, обновляет и удаляет пары вопросов и ответов. 

```csharp
async static Task<Response> GetStatus(string operation)
{
    string uri = host + service + operation;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Этот вызов API возвращает ответ JSON, который содержит состояние операции: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Повторяйте вызов до успешного или неудачного выполнения: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Добавление метода UpdateKB
Следующий метод обновляет базы знаний и повторяет проверки состояния. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ.

```csharp
async static void UpdateKB(string kb, string new_kb)
{
    try
    {
        // Starts the QnA Maker operation to update the knowledge base.
        var response = await PatchUpdateKB(kb, new_kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostUpdateKB(string, string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation updating the
        // knowledge base. Once the operation state is something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);
            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still updating the knowledge base. The thread is
                // paused for a number of seconds equal to the Retry-After
                // header value, and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed updating the knowledge base.
                done = true;
            }
        }
    }
    catch(Exception ex)
    {
        // An error occurred while updating the knowledge base. Ensure that
        // you included your QnA Maker subscription key and knowledge base ID
        // where directed in the sample.
        Console.WriteLine("An error occurred while updating the knowledge base." + ex.InnerException);
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }
}
```

## <a name="add-the-updatekb-method-to-main"></a>Добавление метода UpdateKB в Main

Измените метод Main, чтобы вызвать метод UpdateKB:

```csharp
static void Main(string[] args)
{
    // Invoke the UpdateKB() method to update a knowledge base, periodically
    // checking the status of the QnA Maker operation until the
    // knowledge base is updated.
    UpdateKB(kb, new_kb);

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```


## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы. Она будет автоматически отправлять запрос к API службы QnA Maker для обновления базы знаний. Затем она будет выполнять опрос на наличие результатов каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

Обновленную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
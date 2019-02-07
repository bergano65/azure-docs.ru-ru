---
title: LUIS и QnA Maker — интеграция с ботом
titleSuffix: Azure Cognitive Services
description: По мере роста базы знаний QnA Maker становится трудно поддерживать ее как единый монолитный набор, поэтому базу знаний необходимо разделить на маленькие логические блоки.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 416295b9dc7736d66515ebcbf9caa52053027c85
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489960"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Использование бота с QnA Maker и LUIS для распределения базы знаний
По мере роста базы знаний QnA Maker становится трудно поддерживать ее как единый монолитный набор, поэтому базу знаний необходимо разделить на маленькие логические блоки.

Хотя в QnA Maker просто создать несколько баз знаний, понадобится элемент логики для направления входящего вопроса в соответствующую базу знаний. Это можно сделать с помощью API распознавания речи.

В этой статье используется пакет SDK для Bot Framework версии 3. См. дополнительные сведения о [пакете SDK для Bot Framework версии 4](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Архитектура

![Архитектура API распознавания речи службы QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

В вышеприведенном сценарии показано, что из модели API распознавания речи (LUIS) сначала QnA Maker получает намерение входящего вопроса, а затем использует его, чтобы направить в правильную базу знаний QnA Maker.

## <a name="create-a-luis-app"></a>Создание приложения LUIS

1. Войдите на портал [LUIS](https://www.luis.ai/).
1. [Создайте приложение](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Добавьте намерение](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) к каждой базе знаний QnA Maker. Примеры высказываний должны соответствовать вопросам в базе знаний QnA Maker.
1. В своем приложении LUIS выполните [обучение приложения LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) и [публикацию приложения LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Обратите внимание на идентификатор приложения LUIS, ключ конечной точки LUIS и регион размещения в разделе **Управление**. Эти значения потребуются позже. 

## <a name="create-qna-maker-knowledge-bases"></a>Создание баз знаний QnA Maker

1. Войдите в [QnA Maker](https://qnamaker.ai).
1. [Создайте](https://www.qnamaker.ai/Create) базу знаний для каждого намерения в приложении LUIS.
1. Проверьте и опубликуйте базы знаний. Выполняя публикацию каждой из баз знаний, необходимо обратить внимание на ее идентификатор, размещение (поддомен, который находится перед _.azurewebsites.net/qnamaker_) и ключ конечной точки авторизации. Эти значения потребуются позже. 

    В этой статье предполагается, что все базы знаний созданы в одной подписке Azure QnA Maker.

    ![Запрос HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Бот веб-приложения

1. [Создайте бот веб-приложения](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) с помощью шаблона LUIS. Выберите пакет SDK версии 3.x и C# в качестве языка программирования.

1. После создания бота веб-приложения выберите его на портале Azure.
1. Выберите **Параметры приложения** на странице навигации службы бота веб-приложения, затем прокрутите вниз до раздела с доступными параметрами **Параметры приложения**.
1. Измените значение параметра **LuisAppId** на значение приложения LUIS, созданного в предыдущем разделе, а затем нажмите кнопку **Сохранить**.


## <a name="change-code-in-basicluisdialogcs"></a>Изменение код в файле BasicLuisDialog.cs
1. В разделе **Управление ботом** на странице навигации бота веб-приложения портала Azure выберите **Сборка**.
2. Выберите **Открыть сетевой редактор кода**. Откроется новая вкладка браузера со средой для редактирования в сети. 
3. В разделе **WWWROOT** выберите каталог **Диалоговые окна**, а затем откройте файл **BasicLuisDialog.cs**.
4. Добавьте зависимости в верхнюю часть файла **BasicLuisDialog.cs**.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Чтобы выполнить десериализацию ответа QnA Maker, необходимо добавить классы, приведенные ниже.

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Чтобы выполнить HTTP-запрос в службу QnA Maker, добавьте следующие классы. Обратите внимание, что в значение заголовка **Авторизация** включено слово `EndpointKey` с пробелом, следующим за ним. Результат JSON будет десерилизирован на предыдущие классы и отправлен в качестве первого ответа.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Изменение класса BasicLuisDialog. Каждое намерение LUIS должно содержать метод, снабженный префиксом **LuisIntent**. Оформленный параметр является актуальным именем намерения LUIS. Имя оформленного метода _должно_ содержать имя намерения LUIS, предназначенного для улучшения читаемости и удобства поддержки, и в то же время должно отличаться на этапах создания и выполнения.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Сборка бота
1. В редакторе кода щелкните правой кнопкой мыши `build.cmd` и выберите пункт **Run from Console** (Запустить из консоли).

    ![запуск из консоли](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Представление кода заменяется окном терминала, в котором отображаются ход и результаты сборки.

    ![сборка консоли](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Тестирование бота
На портале Azure выберите **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. Введите сообщения с разными намерениями, чтобы получить ответ соответствующей базы знаний.

![тест веб-чата](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Create a business continuity plan for your QnA Maker service](../How-To/business-continuity-plan.md) (Создание плана по обеспечению непрерывности бизнеса для службы QnA Maker)

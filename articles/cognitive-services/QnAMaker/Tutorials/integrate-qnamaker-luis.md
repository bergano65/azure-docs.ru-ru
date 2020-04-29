---
title: Интеграция LUIS и QnAMaker-Bot
titleSuffix: Azure Cognitive Services
description: По мере роста базы знаний QnA Maker становится трудно поддерживать ее как единый монолитный набор. Разделите базу знаний на логические фрагменты меньшего размера.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402719"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Использование программы Bot с QnA Maker и LUIS для распространения базы знаний
По мере роста базы знаний QnA Maker становится трудно поддерживать ее как единый монолитный набор. Разделите базу знаний на логические фрагменты меньшего размера.

Хотя создание нескольких баз знаний в QnA Maker несложно, вам потребуется логика для направления входящего вопроса в соответствующую базу знаний. Это можно сделать с помощью API распознавания речи.

В этой статье используется пакет SDK для Bot Framework версии 3. Если вы заинтересованы в версии пакета SDK для Bot Framework версии 4, см. раздел [Использование нескольких моделей Luis и QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architecture

![График, иллюстрирующий архитектуру QnA Maker с Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

На предыдущем рисунке показано, что QnA Maker сначала получает цель входящего вопроса из модели LUIS. Затем QnA Maker использует эту цель для направления вопроса в правильную базу знаний QnA Maker.

## <a name="create-a-luis-app"></a>Создание приложения LUIS

1. Войдите на портал [Luis](https://www.luis.ai/) .
1. [Создайте приложение](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Добавьте намерение](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) к каждой базе знаний QnA Maker. Примеры высказываний должны соответствовать вопросам в базе знаний QnA Maker.
1. [Обучить приложение Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) и [опубликуйте приложение Luis](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. В разделе **Управление** запишите идентификатор приложения Luis, ключ КОНЕЧНОЙ точки Luis и [имя пользовательского домена](../../cognitive-services-custom-subdomains.md). Эти значения потребуются позже.

## <a name="create-qna-maker-knowledge-bases"></a>Создание баз знаний QnA Maker

1. Войдите в [QnA Maker](https://qnamaker.ai).
1. [Создайте](https://www.qnamaker.ai/Create) базу знаний для каждой цели в приложении Luis.
1. Проверьте и опубликуйте базы знаний. При публикации каждого из них запишите идентификатор, имя ресурса (пользовательский поддомен до _. azurewebsites.NET/qnamaker_) и ключ конечной точки авторизации. Эти значения потребуются позже.

    В этой статье предполагается, что базы знаний созданы в одной и той же подписке Azure QnA Maker.

    ![Снимок экрана QnA Maker HTTP-запроса](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Бот веб-приложения

1. [Создайте "Basic" Bot с помощью службы Azure Bot](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), которая автоматически включает приложение Luis. Выберите язык программирования C#.

1. После создания робота веб-приложения в портал Azure выберите робот веб-приложения.
1. В области навигации службы веб-приложения Bot выберите **Параметры приложения**. Затем прокрутите вниз до раздела **Параметры приложения** доступных параметров.
1. Измените **луисаппид** на значение приложения Luis, созданного в предыдущем разделе. Затем нажмите кнопку **Save** (Сохранить).


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Изменение кода в файле BasicLuisDialog.cs
1. В разделе **Управление ботом** на странице навигации бота веб-приложения портала Azure выберите **Сборка**.
2. Выберите **Открыть сетевой редактор кода**. Откроется новая вкладка браузера со средой для редактирования в сети.
3. В разделе **wwwroot** выберите каталог **диалоговых окон** , а затем откройте **BasicLuisDialog.CS**.
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

5. Добавьте следующие классы для десериализации QnA Maker ответа:

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


6. Чтобы выполнить HTTP-запрос в службу QnA Maker, добавьте следующие классы. Обратите внимание, что значение заголовка **авторизации** включает слово, `EndpointKey`и пробел после слова. Результат JSON десериализуется в предыдущие классы, и возвращается первый ответ.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
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
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
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


7. Измените `BasicLuisDialog` класс. Каждое намерение LUIS должно содержать метод, снабженный префиксом **LuisIntent**. Оформленный параметр является актуальным именем намерения LUIS. Декорированное имя метода _должно_ быть именем цели Luis для удобства чтения и сопровождения, но не обязательно должно совпадать со структурой или средой выполнения.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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
1. В редакторе кода щелкните правой кнопкой мыши **сборку. cmd**и выберите команду **запустить из консоли**.

    ![Снимок экрана с параметром "запустить из консоли" в редакторе кода](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Представление кода заменяется окном терминала, которое показывает ход выполнения и результаты сборки.

    ![Снимок экрана: построение консоли](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Тестирование бота
На портале Azure выберите **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. Введите сообщения с разными намерениями, чтобы получить ответ соответствующей базы знаний.

![Снимок экрана теста веб-чата](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Интеграция базы знаний с агентом в виртуальные агенты питания](integrate-with-power-virtual-assistant-fallback-topic.md)

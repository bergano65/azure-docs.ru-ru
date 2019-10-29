---
title: Руководство по Создание, публикация и получение ответа — QnA Maker
titleSuffix: Azure Cognitive Services
description: В этом руководстве с использованием REST содержатся сведения о программном создании и публикации базы знаний, а также о создании ответа на вопрос из базы знаний.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/12/2019
ms.author: diberry
ms.openlocfilehash: d13bce3c1cafd20b311aa882d3a32101c1833ba5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555507"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Руководство. Создание базы знаний и получение ответа на вопрос с использованием C#

В этом руководстве вы научитесь программно создавать и публиковать базу знаний, а затем отвечать на вопрос клиента с помощью этой базы. 

> [!div class="checklist"]
> * Создание базы знаний 
> * Проверка состояния создания.
> * Обучение и публикация базы знаний.
> * Получение сведений о конечных точках.
> * Использование cURL для отправки запроса в базу знаний


В этом кратком руководстве демонстрируется вызов REST API службы QnA Maker:

* [Создание базы знаний](https://go.microsoft.com/fwlink/?linkid=2092179).
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).
* [Получение сведений о базе знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails). 
* [Получение конечных точек базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys).
* [Опубликовать](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Предварительные требования

* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и имя ресурса, на портале Azure для своего ресурса QnA Maker выберите **Быстрое начало**. 

> [!NOTE] 
> Полные файлы решения доступны в [репозитории GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Создание проекта базы знаний

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В начале файла Program.cs замените одиночный оператор _using_ следующими строками, чтобы добавить необходимые зависимости в проект:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Добавление класса KBDetails
Добавьте класс KBDetails внутрь скобок пространства имен. Этот класс позволяет библиотеке NewtonSoft десериализовать ответ JSON в класс C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант

В начало класса Program добавьте следующие константы для доступа к API службы QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Добавление определения базы знаний

После констант добавьте приведенное ниже определение модели базы знаний.

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Добавление вспомогательных функций и структур
Добавьте следующие строки кода внутри класса Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Добавление запроса POST для создания базы знаний

Приведенный ниже код отправляет HTTPS-запрос к API службы QnA Maker на создание базы знаний. Возвращается следующий ответ:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Этот вызов API возвращает ответ JSON, который содержит идентификатор операции. Позже программа с помощью этого идентификатора определит, успешно ли создана база знаний. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Добавление запроса GET для определения состояния создания

Проверьте состояние операции создания.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Этот вызов API возвращает ответ JSON, который содержит состояние операции: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Добавление метода CreateKB

Следующий метод инкапсулирует вызовы для создания базы знаний и проверки состояния.  **Идентификатор операции** _создания_ возвращается в **расположение** поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ. Когда операция завершается успешно, идентификатор базы знаний возвращается в **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Добавление метода публикации

Когда база данных будет успешно создана, опубликуйте ее. Возможно, вы ожидали, что необходимо отправить вызов к API обучения. Этого не требуется для данной версии. 

Приведенный ниже код отправляет HTTPS-запрос к API службы QnA Maker на публикацию базы знаний. Возвращается следующий ответ:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

Вызов API возвращает состояние 204 для успешной публикации без содержимого в тексте ответа. Простой пример кода добавляет текст для ответов 204, чтобы вы могли видеть результат.

Этот ответ возвращается без изменений для любого другого ответа.

## <a name="generating-an-answer"></a>Создание ответа
Чтобы обращаться к базе знаний для отправки вопроса и получения наилучшего ответа, программе требуется _имя ресурса_ из API сведений о базе знаний и _ключ первичной конечной точки_ из API конечных точек. Эти методы описаны в следующих разделах вместе с методом создания ответа. 

В следующей таблице показано использование данных для создания URI:

|Создание шаблона URI ответа|
|--|
|https://**имя_ресурса**.azurewebsites.net/qnamaker/knowledgebases/**ИД_базы_знаний**/generateAnswer|

_Первичная конечная точка_ передается как заголовок для проверки подлинности запроса на создание ответа:

|Имя заголовка|Значение заголовка|
|--|--|
|Авторизация|`Endpoint` + **первичная конечная точка**<br>Пример: `Endpoint xxxxxxx`<br>Обратите внимание на пробел между текстом `Endpoint` и значением первичной конечной точки. 

Текст запроса должен передать соответствующий JSON:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Получение сведений о базе знаний
Добавьте следующий метод, чтобы получить сведения о базе знаний. Эти сведения содержат имя ресурса базы знаний, известное как `hostName` в приведенном ниже коде JSON. Имя ресурса — это имя, которое вы ввели при создании ресурса QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Этот вызов API возвращает ответ JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Получение конечных точек базы знаний
Добавьте следующий метод для получения первичных конечных точек QnA Maker. Эти конечные точки не привязаны к базе знаний. Они допустимы для всех баз знаний, связанных с ключами ресурса QnA Maker на портале Azure.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Этот вызов API возвращает ответ JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Получение ответа
Добавьте следующий метод, чтобы получить ответ на вопрос пользователя. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Если вы хотите ограничить ответ на вопросы, добавьте к тексту свойство `[rankerType](Learn more about [rankerType](../concepts/best-practices.md#choosing-ranker-type).
)`, например: 

```csharp
request.Content = new StringContent("{question:'" + question + "', rankerType:'QuestionOnly'}", Encoding.UTF8, "application/json"); 
```

Этот вызов API возвращает ответ JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Метод Main
Метод Main показывает синхронные вызовы для создания и публикации ответа. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы. 

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. Теперь, когда вы знаете, как использовать API для создания ответа, вы можете применять его с любым языком или платформой запросов HTTP. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))

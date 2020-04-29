---
title: 'Краткое руководство: создание базы знаний с использованием REST, C# — QnA Maker'
description: Из этого краткого руководства вы узнаете, как создать пример базы знаний QnA Maker программными средствами с использованием REST на C#, чтобы отобразить ее на панели мониторинга Azure в учетной записи API Cognitive Services.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 78608d3acdfea07f21ccc42e8d530ff502fbb0b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851930"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью C# и функции "ОСТАВШАЯся"

В этом кратком руководстве описано, как программным способом создать и опубликовать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/knowledge-base.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | —[пример C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* У вас должен быть [ресурс QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе.

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `qna-maker-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```dotnetcli
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В начале файла Program.cs замените одиночный оператор using следующими строками, чтобы добавить необходимые зависимости в проект:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант

В верхней части класса Program добавьте необходимые константы для доступа к QnA Maker.

Настройте следующие значения переменных среды.

* `QNA_MAKER_SUBSCRIPTION_KEY`. **Ключ** — это строка из 32 символов, которая доступна на странице "Быстрый запуск" ресурса QnA Maker на портале Azure. Это не то же самое, что ключ конечной точки прогнозирования.
* `QNA_MAKER_ENDPOINT`. **Конечная точка** — это URL-адрес для разработки в формате `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Это не тот же URL-адрес, который используется для запроса конечной точки прогнозирования.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-26 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Добавление определения базы знаний

После констант добавьте приведенное ниже определение базы знаний:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=28-58 "Add the knowledge base definition")]

## <a name="add-supporting-functions-and-structures"></a>Добавление вспомогательных функций и структур
Добавьте следующие строки кода внутри класса Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=60-99 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Добавление запроса POST для создания базы знаний

Приведенный ниже код отправляет HTTPS-запрос к API службы QnA Maker на создание базы знаний. Возвращается следующий ответ:

[!code-csharp[Add PostCreateKB to request via POST](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=145-165 "Add PostCreateKB to request via POST")]

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=101-122 "Add a POST request to create KB")]

Этот вызов API возвращает ответ JSON, который содержит идентификатор операции. С помощью этого идентификатора определите, успешно ли создана база знаний.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Добавление запроса GET для определения состояния создания

Проверьте состояние операции.

[!code-csharp[Add GetStatus to request via GET](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=167-187 "Add GetStatus to request via GET")]

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=124-143 "Add GET request to determine creation status")]

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

## <a name="add-createkb-method"></a>Добавление метода CreateKB

Следующий метод создает базу знаний и повторяет проверки состояния.  **Идентификатор операции** _создания_ возвращается в **расположении**поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ. Когда операция завершается успешно, идентификатор базы знаний возвращается в **resourceLocation**.

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=189-254 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Добавление метода CreateKB в Main

Измените метод Main, чтобы вызвать метод CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=256-265 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы. Она будет автоматически отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет выполнять опрос на наличие результатов каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker.


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
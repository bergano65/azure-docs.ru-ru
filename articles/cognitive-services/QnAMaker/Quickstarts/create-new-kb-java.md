---
title: Краткое руководство. Создание базы знаний с использованием REST, Java — QnA Maker
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве по Java REST описывается, как создать пример базы знаний QnA Maker программными средствами с использованием REST, чтобы отобразить ее на панели мониторинга Azure в вашей учетной записи API Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: a8543d4fb0819b31b1f372fad2b2aced44b327f1
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730430"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью Java

В этом кратком руководстве описано, как программным способом создать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/data-sources-supported.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Создание файла для базы знаний

Создайте файл с именем `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `CreateKB.java` укажите следующие строки:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы в класс `CreateKB`, чтобы получить доступ к QnA Maker. Замените значение переменной `subscriptionKey` собственным ключом QnA Maker. Не нужно добавлять закрывающую фигурную скобку, чтобы закрыть класс, так как она находится в последнем фрагменте кода в конце этого руководства.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Добавление классов определения модели базы знаний
После констант добавьте следующие классы и функции в класс `CreateKB` для сериализации объекта определения модели в JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Добавление вспомогательных функций

Далее добавьте приведенные ниже функции поддержки в класс `CreateKB`.

1. Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Добавьте следующий класс, чтобы управлять HTTP-запросом:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Добавьте приведенный ниже метод, который создает POST-запросы к API-интерфейсам QnA Maker. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Добавьте приведенный ниже метод, который создает GET-запросы к API-интерфейсам QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Добавление метода, который создает базу знаний
Добавьте следующий метод, который создает базу знаний, вызвав метод Post.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

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

## <a name="add-a-method-to-get-status"></a>Добавление метода для получения состояния
Добавьте следующий метод для проверки состояния создания.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

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

## <a name="add-a-main-method"></a>Добавление метода main
Метод main создает базу знаний, а затем выполняет опрос состояний. **Идентификатор операции** создания возвращается в **расположение** поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. ** Цикл `while` будет выполняться до тех пор, пока состояние не станет завершенным.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Выполнение сборки и запуск программы

1. Убедитесь, что библиотека gson находится в каталоге `./libs`. Скомпилируйте файл `CreateKB.java` в командной строке:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. В командной строке введите приведенную ниже команду, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))

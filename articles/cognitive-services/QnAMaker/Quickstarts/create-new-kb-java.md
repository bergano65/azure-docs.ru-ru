---
title: 'Краткое руководство: создание базы знаний с использованием REST, Java — QnA Maker'
description: В этом кратком руководстве по технологиям Java для записи вы узнаете, как создать пример базы знаний QnA Maker, который будет отображаться на панели мониторинга Azure вашей учетной записью API Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 275b1976e51789d4a458e7bd9f7cfa3240cc81d5
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777663"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Краткое руководство. Создание базы знаний в QnA Maker с использованием Java

В этом кратком руководстве описано, как программным способом создать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/knowledge-base.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Пример для Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>предварительные требования

* [Go 1.10.1.](https://golang.org/dl/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

[Пример кода](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) доступен в репозитории GitHub для QnA Maker на Java.

## <a name="create-a-knowledge-base-file"></a>Создание файла для базы знаний

Создайте файл с именем `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `CreateKB.java` укажите следующие строки:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы в класс `CreateKB`, чтобы получить доступ к QnA Maker.

У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и имя ресурса, на портале Azure для своего ресурса QnA Maker выберите **Быстрое начало**.

Задайте следующие значения:

* `<your-qna-maker-subscription-key>`. **Ключ** — это строка из 32 символов, которая доступна на странице "Быстрый запуск" ресурса QnA Maker на портале Azure. Этот ключ не совпадает с ключом конечной точки прогнозирования.
* `<your-resource-name>`. **Имя ресурса** используется для создания URL-адреса конечной точки разработки в формате `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Это имя ресурса отличается от имени, используемого для запроса конечной точки прогнозирования.

Не нужно добавлять закрывающую фигурную скобку, чтобы закрыть класс, так как она находится в последнем фрагменте кода в конце этого руководства.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Добавление классов определения модели базы знаний
После констант добавьте следующие классы и функции в класс `CreateKB` для сериализации объекта определения модели в JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Добавление вспомогательных функций

Далее добавьте приведенные ниже функции поддержки в класс `CreateKB`.

1. Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Добавьте следующий класс, чтобы управлять HTTP-запросом:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Добавьте приведенный ниже метод, который создает POST-запросы к API-интерфейсам QnA Maker. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Добавьте приведенный ниже метод, который создает GET-запросы к API-интерфейсам QnA Maker.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Добавление метода, который создает базу знаний
Добавьте следующий метод, который создает базу знаний, вызвав метод Post.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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
Метод main создает базу знаний, а затем выполняет опрос состояний. Идентификатор операции возвращается в **расположение** поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. Цикл `while` будет выполняться до тех пор, пока состояние не станет завершенным.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Выполнение сборки и запуск программы

1. Убедитесь, что библиотека gson находится в каталоге `./libs`. В командной строке Скомпилируйте файл `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Введите следующую команду в командной строке, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))

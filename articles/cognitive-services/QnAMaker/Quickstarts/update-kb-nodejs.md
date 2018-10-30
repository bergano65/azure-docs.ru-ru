---
title: Краткое руководство. Обновление базы знаний QnA Maker с использованием Node.js и REST
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как программным способом обновить имеющуюся базу знаний QnA Maker.  Этот код JSON позволяет обновить базу знаний путем добавления, изменения или удаления источников данных.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647822"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>Краткое руководство. Обновление базы знаний в QnA Maker с помощью Node.js

В этом кратком руководстве описано, как программным способом обновить имеющуюся базу знаний QnA Maker.  Этот код JSON позволяет обновить базу знаний путем добавления, изменения или удаления источников данных.

Этот API является эквивалентом изменения и нажатия кнопки **Save and train** (Сохранение и обучение) на портале QnA Maker.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Обновление.](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API. 
* [Получение сведений об операции](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails).

## <a name="prerequisites"></a>Предварительные требования

* [Node.js 6+](https://nodejs.org/en/download/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 
* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса kbid, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Если у вас еще нет базы знаний, создайте пример для этого краткого руководства по инструкциям из [этой статьи](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Создание файла базы знаний Node.js

Создайте файл с именем `update-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `update-knowledge-base.js` укажите следующие строки:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Замените значение переменной `subscriptionKey` собственным ключом QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Добавление идентификатора базы знаний

После определения констант далее необходимо добавить идентификатор базы знаний и указать его в пути:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>Добавление определения модели обновления базы знаний

После констант добавьте следующее определение обновления базы знаний. Это определение содержит три раздела:

* добавление
* обновить
* удалить

Каждый раздел может использоваться в одном запросе к API. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Добавление вспомогательных функций

Далее необходимо добавить приведенные ниже вспомогательные функции.

1. Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Добавьте следующие функции, чтобы управлять HTTP-ответом и получить состояние операции создания:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>Добавление запроса PATCH для обновления базы знаний

Добавьте приведенные ниже функции, чтобы отправить HTTP-запрос PATCH на обновление базы знаний. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

Этот вызов API возвращает ответ JSON, который содержит идентификатор операции. Этот идентификатор используется во время запроса состояния, если операция не завершена.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>Добавление запроса GET для определения состояния операции

Проверьте состояние операции.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

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

## <a name="add-updatekb-method"></a>Добавление метода update_kb

Следующий метод обновляет базы знаний и повторяет проверки состояния. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>Запуск программы

В командной строке введите приведенную ниже команду, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для обновления базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

```bash
node update-knowledge-base.js
```

Обновленную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
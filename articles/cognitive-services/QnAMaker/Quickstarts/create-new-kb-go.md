---
title: Краткое руководство. Создание базы знаний QnA Maker с использованием REST и Go
titlesuffix: Azure Cognitive Services
description: Из этого краткого руководства по использованию REST в Go вы узнаете, как программным способом создать пример базы знаний QnA Maker, которая затем появится на панели мониторинга Azure в вашей учетной записи API Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 4831fca8a72f62d64c1977b510f039065f6cd3c5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788566"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью Go

В этом кратком руководстве описано, как программным способом создать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/data-sources-supported.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API. 

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://go.microsoft.com/fwlink/?linkid=2092179).
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).

## <a name="prerequisites"></a>Предварительные требования

* [Go 1.10.1](https://golang.org/dl/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 

## <a name="create-a-knowledge-base-go-file"></a>Создание файла базы знаний на Go

Создайте файл с именем `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `create-new-knowledge-base.go` укажите следующие строки:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Замените значение переменной `subscriptionKey` собственным ключом QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Добавление определения модели базы знаний
После констант добавьте приведенное ниже определение модели базы знаний. После определения модель преобразуется в строку.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Добавление вспомогательных структур и функций

Далее необходимо добавить приведенные ниже вспомогательные функции.

1. Добавьте структуру для HTTP-запроса.

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Добавьте приведенный ниже метод для обработки POST-запросов к API-интерфейсам QnA Maker. В этом руководстве POST используется для отправки определения базы знаний в QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Добавьте приведенный ниже метод для обработки GET-запросов к API-интерфейсам QnA Maker. В этом руководстве GET используется для проверки состояния операции создания. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Добавление функции для создания базы знаний

Добавьте приведенные ниже функции, чтобы отправить HTTP-запрос POST на создание базы знаний. **Идентификатор операции** _создания_ возвращается в **расположение** поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>Добавление функции для получения сведений о состоянии

Добавьте приведенную ниже функцию, чтобы отправить HTTP-запрос GET на проверку состояния операции. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Добавление функции main

Ниже приведена функция main, которая создает базу данных и повторяет проверки состояния. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Компиляция программы
Чтобы скомпилировать файл, выполните следующую команду: Командная строка не возвращает никаких данных в случае успешной компиляции.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Запуск программы

В командной строке введите приведенную ниже команду, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

```bash
go run create-new-knowledge-base
```

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))

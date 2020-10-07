---
title: Краткое руководство. Создание базы знаний — REST, Go — QnA Maker
description: Из этого краткого руководства по использованию REST в Go вы узнаете, как программным способом создать пример базы знаний QnA Maker, которая затем появится на панели мониторинга Azure в вашей учетной записи API Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: be314ad74fbb58709f5707e84b193f8ca69b47d9
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777694"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью Go

В этом кратком руководстве описано, как программным способом создать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/knowledge-base.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Пример Go](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>предварительные требования

* [Go 1.10.1.](https://golang.org/dl/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

## <a name="create-a-knowledge-base-go-file"></a>Создание файла базы знаний на Go

Создайте файл с именем `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `create-new-knowledge-base.go` укажите следующие строки:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Добавление определения модели базы знаний
После констант добавьте приведенное ниже определение модели базы знаний. После определения модель преобразуется в строку.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Добавление вспомогательных структур и функций

Далее необходимо добавить приведенные ниже вспомогательные функции.

1. Добавьте структуру для HTTP-ответа:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Добавьте приведенный ниже метод для обработки POST-запросов к API-интерфейсам QnA Maker. В этом руководстве POST используется для отправки определения базы знаний в QnA Maker.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Добавьте приведенный ниже метод для обработки GET-запросов к API-интерфейсам QnA Maker. В этом руководстве GET используется для проверки состояния операции создания.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Добавление функции для создания базы знаний

Добавьте приведенные ниже функции, чтобы отправить HTTP-запрос POST на создание базы знаний. **Идентификатор операции** _создания_ возвращается в **расположении**поля заголовка ответа POST, а затем используется как часть маршрута в запросе GET. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))

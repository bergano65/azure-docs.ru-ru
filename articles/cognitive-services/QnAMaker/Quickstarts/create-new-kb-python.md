---
title: Краткое руководство. Создание базы знаний — REST, Python — QnA Maker
description: Из этого краткого руководства по использованию REST в Python вы узнаете, как программным способом создать пример базы знаний QnA Maker, которая затем появится на панели мониторинга Azure в вашей учетной записи API Cognitive Services.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: afee82b66f9803333e27f029ecb487a47ba5dd9e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259733"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью Python

В этом кратком руководстве описано, как программным способом создать и опубликовать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/knowledge-base.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Получение сведений об операции](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Справочная документация](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Пример Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* [Python 3.7](https://www.python.org/downloads/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

## <a name="create-a-knowledge-base-python-file"></a>Создание файла базы знаний на Python

Создайте файл с именем `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `create-new-knowledge-base-3x.py` укажите следующие строки:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Замените значения `<your-qna-maker-subscription-key>` и `<your-resource-name>` собственным ключом и именем ресурса QnA Maker.

В верхней части класса Program добавьте необходимые константы для доступа к QnA Maker.

Задайте следующие значения:

* `<your-qna-maker-subscription-key>`. **Ключ** — это строка из 32 символов, которая доступна на странице "Быстрый запуск" ресурса QnA Maker на портале Azure. Это не то же самое, что ключ конечной точки прогнозирования.
* `<your-resource-name>`. **Имя ресурса** используется для создания URL-адреса конечной точки разработки в формате `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Это не тот же URL-адрес, который используется для запроса конечной точки прогнозирования.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>Добавление определения модели базы знаний

После констант добавьте приведенное ниже определение модели базы знаний. После определения модель преобразуется в строку.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>Добавление вспомогательных функций

Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>Добавление функции для создания базы знаний

Добавьте приведенную ниже функцию, чтобы отправить HTTP-запрос POST на создание базы знаний.
Этот вызов API возвращает ответ JSON, в заголовке которого в поле **Location** содержится идентификатор операции. С помощью этого идентификатора определите, успешно ли создана база знаний. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

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

## <a name="add-function-to-check-creation-status"></a>Добавление функции для проверки состояния создания

Приведенная ниже функция проверяет состояние создания, отправляя идентификатор операции в конце маршрута URL-адреса. Вызов `check_status` находится внутри основного цикла _while_.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

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

## <a name="add-main-code-block"></a>Добавление основного блока кода
Следующий цикл периодически запрашивает состояние операции создания до ее завершения.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

В командной строке введите приведенную ниже команду, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

```bash
python create-new-knowledge-base-3x.py
```

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. Выберите имя базы знаний, например QnA Maker FAQ, чтобы просмотреть ее.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))

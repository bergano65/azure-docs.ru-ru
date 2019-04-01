---
title: Определения рабочих процессов "умеренность" с помощью REST API консоли - Content Moderator
titlesuffix: Azure Cognitive Services
description: Интерфейсы API проверки Azure модератора содержимого можно использовать для определения пользовательских рабочих процессов и пороговые значения на основе содержимого политик.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756586"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Определение и использование рабочих процессов "умеренность" (REST)

Рабочие процессы, облачные настраиваемые фильтры, которые можно использовать для обработки содержимого более эффективно. Рабочие процессы могут подключаться к различным службам для фильтрации содержимого по-разному и предпринять соответствующие действия. В этом руководстве показано, как использовать интерфейсы REST API, рабочий процесс через консоль API для создания и использования рабочих процессов. Поняв структуру API-интерфейсы, легко можно перенести эти вызовы на любую платформу совместимые с REST.

## <a name="prerequisites"></a>Технические условия

- Войдите или создайте учетную запись на Content Moderator [средство проверки](https://contentmoderator.cognitive.microsoft.com/) сайта.

## <a name="create-a-workflow"></a>Создание рабочего процесса

Чтобы создать или обновить рабочий процесс, перейдите к **[рабочего процесса — создать или обновить](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API ссылку на страницу и нажмите кнопку для вашего ключа региона (его можно найти в URL-адрес конечной точки на **учетные данные**  странице [средство проверки](https://contentmoderator.cognitive.microsoft.com/)). Это запускает консоль API, где можно легко создать и выполнить вызовы REST API.

![Выбор региона на странице "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите значения для **team**, **workflowname**, и **Ocp-Apim-Subscription-Key**:

- **team**: Идентификатор команды, созданный при настройке вашего [средство проверки](https://contentmoderator.cognitive.microsoft.com/) учетной записи (в **идентификатор** на экран ввода учетных данных средства проверки).
- **workflowName**: Имя нового рабочего процесса для добавления (или существующее имя, если вы хотите обновить существующий рабочий процесс).
- **Ocp-Apim-Subscription-Key**: Ваш ключ Content Moderator. Его можно найти на **параметры** вкладке [средство проверки](https://contentmoderator.cognitive.microsoft.com).

![Параметры запроса и заголовки в консоли "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Введите определение рабочего процесса

1. Изменить **текст запроса** поле для ввода запроса JSON со сведениями для **описание** и **тип** (либо `Image` или `Text`).
2. Для **выражение**, копирования рабочего процесса по умолчанию выражение JSON. Окончательный строка JSON должен выглядеть следующим образом:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Простые, сложные и даже вложенные выражения можно определить для рабочих процессов, с помощью этого API. [Рабочего процесса — создание или обновление](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) документации включает примеры более сложная логика.

### <a name="submit-your-request"></a>Отправка запроса
  
Нажмите кнопку **Отправить**. Если операция выполнена успешно, **Response status** (Состояние ответа) имеет значение `200 OK`, а в поле **Response content** (Содержимое ответа) отображается `true`.

### <a name="examine-the-new-workflow"></a>Изучите новый рабочий процесс

В [средство проверки](https://contentmoderator.cognitive.microsoft.com/)выберите **параметры** > **рабочие процессы**. В списке появится новый рабочий процесс.

![Список рабочих процессов в инструменте проверки](images/workflow-console-new-workflow.PNG)

Выберите **изменить** параметр для рабочего процесса и перейти к **конструктор** вкладки. Вы видите это интуитивно понятный представление логики JSON.

![Вкладка "Designer" (Конструктор) для выбранного рабочего процесса](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Получение сведений о рабочем процессе

Чтобы получить сведения о существующего рабочего процесса, перейдите в раздел **[рабочего процесса — Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API ссылку на страницу и нажмите кнопку "," для вашего региона (регион, в котором осуществляется ключа).

![Выбор региона на странице "Workflow - Get" (Рабочий процесс — получение)](images/test-drive-region.png)

Введите параметры вызова REST, как описано в разделе выше. Убедитесь, что на этот раз **workflowname** имя существующего рабочего процесса.

![Получение параметров запроса и заголовков](images/workflow-get-default.PNG)

Нажмите кнопку **Отправить**. Если операция прошла успешно, **состояние ответа** — `200 OK`и **содержимое ответа** рабочего процесса отображаются в формате JSON, как показано ниже:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как использовать рабочие процессы с [заданиями модерации контента](try-review-api-job.md).
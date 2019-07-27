---
title: Определение рабочих процессов для контроля с помощью консоли REST API Content Moderator
titleSuffix: Azure Cognitive Services
description: Вы можете использовать API-интерфейсы проверки Content Moderator Azure, чтобы определить настраиваемые рабочие процессы и пороговые значения на основе политик содержимого.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 71b7be74ca7b6ac072dfd7c9fa6b8efa72361dfa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561207"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Определение и использование рабочих процессов контроля (остальное)

Рабочие процессы — это настраиваемые фильтры на основе облака, которые можно использовать для более эффективного обработки содержимого. Рабочие процессы могут подключаться к различным службам для фильтрации содержимого различными способами, а затем предпринимать соответствующие действия. В этом руководство показано, как создавать и использовать рабочие процессы с помощью API-интерфейсов RESTFUL рабочего процесса через консоль API. Когда вы понимаете структуру интерфейсов API, вы можете легко перенести эти вызовы на любую платформу, совместимую с остальными частями.

## <a name="prerequisites"></a>предварительные требования

- Войдите или создайте учетную запись на сайте [средства проверки](https://contentmoderator.cognitive.microsoft.com/) Content Moderator.

## <a name="create-a-workflow"></a>Создание рабочего процесса

Чтобы создать или обновить рабочий процесс, перейдите на страницу **[рабочего процесса создание или обновление](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** справочника по API и нажмите кнопку для своего ключевого региона (это можно найти в URL-адресе конечной точки на странице **учетные данные** [средства проверки](https://contentmoderator.cognitive.microsoft.com/)). Запустится консоль API, где можно легко создавать и выполнять вызовы REST API.

![Выбор региона на странице "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова RESTFUL

Введите значения для **Team**, **workflowname**и **OCP-Apim-Subscription-Key**:

- **team**: Идентификатор команды, созданный при настройке учетной записи [средства проверки](https://contentmoderator.cognitive.microsoft.com/) (находится в поле **идентификатор** на экране учетных данных средства проверки).
- **workflowName**: Имя нового рабочего процесса для добавления (или существующего имени, если требуется обновить существующий рабочий процесс).
- **Ocp-Apim-Subscription-Key**: Ключ Content Moderator. Его можно найти на вкладке **Параметры** [средства проверки](https://contentmoderator.cognitive.microsoft.com).

![Параметры запроса и заголовки в консоли "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Введите определение рабочего процесса

1. Измените поле **текст запроса** , чтобы ввести запрос JSON с подробными сведениями о **описании** и `Image` `Text` **типе** (либо).
2. В поле **выражение**СКОПИРУЙТЕ выражение JSON рабочего процесса по умолчанию. Итоговая строка JSON должна выглядеть следующим образом:

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
> С помощью этого API можно определить простые, сложные и даже вложенные выражения для рабочих процессов. В документации по [созданию и обновлению рабочих процессов](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) есть примеры более сложной логики.

### <a name="submit-your-request"></a>Отправка запроса
  
Нажмите кнопку **Отправить**. Если операция выполнена успешно, **Response status** (Состояние ответа) имеет значение `200 OK`, а в поле **Response content** (Содержимое ответа) отображается `true`.

### <a name="examine-the-new-workflow"></a>Изучение нового рабочего процесса

В [средстве проверки](https://contentmoderator.cognitive.microsoft.com/)выберите **Параметры** > **рабочие процессы**. Новый рабочий процесс должен появиться в списке.

![Список рабочих процессов в инструменте проверки](images/workflow-console-new-workflow.PNG)

Выберите параметр **изменить** для рабочего процесса и перейдите на вкладку **конструктор** . Здесь можно увидеть интуитивно понятное представление логики JSON.

![Вкладка "Designer" (Конструктор) для выбранного рабочего процесса](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Получение сведений о рабочем процессе

Чтобы получить сведения о существующем рабочем процессе, перейдите на страницу " **[Рабочий процесс — получение](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** справочника по API" и выберите кнопку для своего региона (регион, в котором находится ваш ключ).

![Выбор региона на странице "Workflow - Get" (Рабочий процесс — получение)](images/test-drive-region.png)

Введите параметры вызова RESTFUL, как показано в приведенном выше разделе. Убедитесь, что в этот раз **workflowname** является именем существующего рабочего процесса.

![Получение параметров запроса и заголовков](images/workflow-get-default.PNG)

Нажмите кнопку **Отправить**. Если операция выполнена, **состояние ответа** — `200 OK`, а в поле ответное **содержимое** отображается рабочий процесс в формате JSON, как показано ниже:

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

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как использовать рабочие процессы с [заданиями модерации контента](try-review-api-job.md).
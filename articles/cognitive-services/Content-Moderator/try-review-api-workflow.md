---
title: Определите рабочие процессы модерации с помощью консоли REST API - Модератор содержимого
titleSuffix: Azure Cognitive Services
description: Можно использовать AAP-обзор ы azure Content Moderator Review для определения пользовательских рабочих процессов и пороговых значений на основе политики содержимого.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754180"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Определение и использование рабочих процессов модерации (REST)

Рабочие процессы — это облачные индивидуальные фильтры, которые можно использовать для более эффективной обработки содержимого. Рабочие процессы могут подключаться к различным службам для фильтрации содержимого различными способами, а затем принять соответствующие меры. В этом руководстве показано, как использовать рабочие aPI REST через консоль API для создания и использования рабочих процессов. Как только вы поймете структуру AIS, вы можете легко портировать эти вызовы на любую платформу, совместимую с REST.

## <a name="prerequisites"></a>Предварительные требования

- Войти или создать учетную запись на сайте [инструмента «Обзор](https://contentmoderator.cognitive.microsoft.com/) модератора контента».

## <a name="create-a-workflow"></a>Создание рабочего процесса

Чтобы создать или обновить рабочий процесс, перейдите на страницу ссылки **[Workflow - Create or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API и выберите кнопку для ключевой области (это можно найти в URL-адресе на странице **инструмента «Проверка»** (на странице [инструмента «Обзор»](https://contentmoderator.cognitive.microsoft.com/)) Это запускает консоль API, где вы можете легко построить и запустить REST API звонки.

![Выбор региона на странице "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите значения для **команды,** **рабочего процесса**и **Ocp-Apim-Subscription-Key:**

- **team**: Идентификатор команды, созданный при настройке учетной записи [инструмента «Обзор»](https://contentmoderator.cognitive.microsoft.com/) (найден в поле **Id** на экране учетных данных инструмента «Обзор»).
- **рабочее название**: Имя нового рабочего процесса для добавления (или существующее имя, если требуется обновить существующий рабочий процесс.
- **Ocp-Apim-Подписка-Ключ**: Ваш ключ модератора контента. Вы можете найти это на вкладке **Настройки** [инструмента Обзор](https://contentmoderator.cognitive.microsoft.com).

![Параметры запроса и заголовки в консоли "Workflow - Create Or Update" (Рабочий процесс — создание или обновление)](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Введите определение рабочего процесса

1. Отспособите поле **тела Запроса,** чтобы ввести запрос JSON `Text`с подробной информацией для **Описания** и **типа** (или `Image` ).
2. Для **выражения**— скопируйте выражение JSON по умолчанию. Ваша последняя строка JSON должна выглядеть следующим образом:

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
> С помощью этого API можно определить простые, сложные и даже вложенные выражения для рабочих процессов. [В документации Workflow - Create or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) есть примеры более сложной логики.

### <a name="submit-your-request"></a>Отправка запроса
  
Выберите **Отправить**. Если операция выполнена успешно, **Response status** (Состояние ответа) имеет значение `200 OK`, а в поле **Response content** (Содержимое ответа) отображается `true`.

### <a name="examine-the-new-workflow"></a>Изучить новый рабочий процесс

В [инструменте «Обзор»](https://contentmoderator.cognitive.microsoft.com/)выберите **рабочие процессы настроек.** > **Workflows** Новый рабочий процесс должен отображаться в списке.

![Список рабочих процессов в инструменте проверки](images/workflow-console-new-workflow.PNG)

Выберите опцию **«Отобрачье»** для рабочего процесса и перейдите на вкладку **«Дизайнер».** Здесь вы можете увидеть интуитивное представление логики JSON.

![Вкладка "Designer" (Конструктор) для выбранного рабочего процесса](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Получение сведений о рабочем процессе

Чтобы получить сведения о существующем рабочем процессе, перейдите на **[справочник Рабочего процесса - Получить](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** справочную страницу API и выбрать кнопку для вашего региона (область, в которой ваш ключ управляется).

![Выбор региона на странице "Workflow - Get" (Рабочий процесс — получение)](images/test-drive-region.png)

Введите параметры вызова REST, как в вышеуказанном разделе. Убедитесь, что на этот раз **имя рабочего процесса** — это имя существующего рабочего процесса.

![Получение параметров запроса и заголовков](images/workflow-get-default.PNG)

Выберите **Отправить**. Если операция выполняется успешно, `200 OK`статус **"Ответ"** отображает рабочий процесс в формате JSON, например: **Response content**

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
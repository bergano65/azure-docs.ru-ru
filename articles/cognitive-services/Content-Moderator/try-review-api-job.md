---
title: Использование заданий "умеренность" с помощью REST API консоли - Content Moderator
titlesuffix: Azure Cognitive Services
description: Используйте операции заданий API проверки для запуска заданий комплексной модерации контента изображений или текста в Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756091"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Определение и использование Модерация заданий (REST)

Задание "умеренность" выступает в качестве оболочки для модерации содержимого, рабочие процессы и проверки функциональности. В этом руководстве показано, как использовать это задание интерфейсов REST API для инициации и проверить задание модерации содержимого. Поняв структуру API-интерфейсы, легко можно перенести эти вызовы на любую платформу совместимые с REST.

## <a name="prerequisites"></a>Технические условия

- Войдите или создайте учетную запись на Content Moderator [средство проверки](https://contentmoderator.cognitive.microsoft.com/) сайта.
- (Необязательно) [Определить пользовательский рабочий процесс](./Review-Tool-User-Guide/Workflows.md) для использования вместе с заданием; также можно использовать рабочий процесс по умолчанию.

## <a name="create-a-job"></a>создать задание;

Чтобы создать задание "умеренность", перейдите к статье [заданий — Создание](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API ссылку на страницу и нажмите кнопку для вашего ключа региона (его можно найти в URL-адрес конечной точки на **учетные данные** странице [проверки средство](https://contentmoderator.cognitive.microsoft.com/)). Это запускает консоль API, где можно легко создать и выполнить вызовы REST API.

![Заданий — Создание страницы Выбор региона](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите следующие значения для создания вызова REST:

- **teamName**: Идентификатор команды, созданный при настройке вашего [средство проверки](https://contentmoderator.cognitive.microsoft.com/) учетной записи (в **идентификатор** на экран ввода учетных данных средства проверки).
- **ContentType**: Это может быть «Image», «Text» или «Видео».
- **ContentId**: Строка пользовательского идентификатора. эта строка передается в API и возвращается с помощью обратного вызова. Это полезно для связывания внутренние идентификаторы или метаданные с результатами задания "умеренность".
- **workflowname**: Имя ранее созданного рабочего процесса (или «default» для рабочего процесса по умолчанию).
- **CallbackEndpoint**: (Необязательно) URL-адрес для получения сведения об обратном вызове, по завершении проверки.
- **Ocp-Apim-Subscription-Key**: Ваш ключ Content Moderator. Его можно найти на **параметры** вкладке [средство проверки](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Введите текст запроса

Текст вызова REST содержит одно поле **ContentValue**. Вставьте содержимое необработанного текста, если Модерация текста, или введите изображения или URL-адрес видео, если вы ведущим изображения и видео. Можно использовать следующий URL-адрес изображения образца: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Параметры запроса, заголовки и поле "Request body" (Текст запроса) в консоли "Job - Create" (Задание — создание)](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Отправка запроса

Нажмите кнопку **Отправить**. Если операция прошла успешно, **состояние ответа** — `200 OK`и **содержимое ответа** поле отображается идентификатор задания. Скопируйте этот идентификатор, чтобы использовать на следующих шагах.

![Идентификатор проверки в поле "Response content" (Содержимое ответа) в консоли "Review - Create" (Проверка — создание)](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Получение состояния задания

Чтобы получить состояние и сведения о выполняющегося или завершенного задания, перейдите к [заданий — получить](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API ссылку на страницу и нажмите кнопку "," для вашего региона (регион, в котором осуществляется ключа).

![Задание — Выбор региона Get](images/test-drive-region.png)

Введите параметры вызова REST, как описано в разделе выше. Для выполнения этого шага **JobId** является строкой уникального идентификатора, полученный при создании задания. Нажмите кнопку **Отправить**. Если операция прошла успешно, **состояние ответа** — `200 OK`и **содержимое ответа** задания отображаются в формате JSON, как показано ниже:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Задания — получение ответа на вызов REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Изучите новые review(s)

При задании содержимого привел к созданию оценки, вы можете просмотреть ее в [средство проверки](https://contentmoderator.cognitive.microsoft.com). Выберите **проверки** > **изображение**/**текст**/**видео** (в зависимости от содержимого, вы используется). Должно отобразиться содержимое, готового к пользовательской проверки. После модератор человека рассматриваются автоматически присвоенных тегов и данных прогноза и отправляет решение окончательный модерации, API заданий отправляет эти данные в конечную точку заданного ответного вызова конечной точки.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как создавать и запрашивать заданий модерации содержимого, с помощью REST API. Далее, интегрируйте задания в сценарий "умеренность" end-to-end, таких как [Модерация Электронная коммерция](./ecommerce-retail-catalog-moderation.md) руководства.
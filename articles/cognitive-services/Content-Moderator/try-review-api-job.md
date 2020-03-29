---
title: Используйте задания модерации с консолью REST API - Модератор контента
titleSuffix: Azure Cognitive Services
description: Используйте операции заданий API проверки для запуска заданий комплексной модерации контента изображений или текста в Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935938"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Определение и использование заданий модерации (REST)

Задание модерации служит своего рода оберткой для функциональности модерации содержимого, рабочих процессов и обзоров. В этом руководстве показано, как использовать задания REST AIS для инициирования и проверки заданий модерации содержимого. Как только вы поймете структуру AIS, вы можете легко портировать эти вызовы на любую платформу, совместимую с REST.

## <a name="prerequisites"></a>Предварительные требования

- Войти или создать учетную запись на сайте [инструмента «Обзор](https://contentmoderator.cognitive.microsoft.com/) модератора контента».
- (Необязательно) [Определите пользовательский рабочий процесс](./Review-Tool-User-Guide/Workflows.md) для использования с вашей работой; вы также можете использовать рабочий процесс по умолчанию.

## <a name="create-a-job"></a>Создание задания

Чтобы создать задание модерации, перейдите на страницу [«Работа - Создайте](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) справочную страницу API» и выберите кнопку для области подписки (это можно найти в URL-адресах на странице **«Учетные данные»** [инструмента «Обзор»).](https://contentmoderator.cognitive.microsoft.com/) Это запускает консоль API, где вы можете легко построить и запустить REST API звонки.

![Выбор региона на странице "Job - Create" (Задание — создание)](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Введите параметры вызова REST

Введите следующие значения для построения вызова REST:

- **TeamName**: Идентификатор команды, созданный при настройке учетной записи [инструмента «Обзор»](https://contentmoderator.cognitive.microsoft.com/) (найден в поле **Id** на экране учетных данных инструмента «Обзор»).
- **ContentType**: Это может быть "Изображение", "Текст" или "Видео".
- **ContentId**: пользовательская строка идентификатора. эта строка передается в API и возвращается с помощью обратного вызова. Это полезно для увязки внутренних идентификаторов или метаданных с результатами задания модерации.
- **Имя рабочего процесса**: Имя рабочего процесса, созданного ранее (или "по умолчанию" для рабочего процесса по умолчанию.
- **Обратный возврат:**(Необязательно) URL для получения информации обратного вызова, когда обзор завершен.
- **Ocp-Apim-Подписка-Ключ**: Ваш ключ модератора контента. Вы можете найти это на вкладке **Настройки** [инструмента Обзор](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Заполните тело запроса

Тело вашего вызова REST содержит одно поле, **ContentValue**. Вставьте в сырое текстовое содержимое, если вы модерируете текст, или введите URL изображения или видео, если вы модерируете изображение/видео. Вы можете использовать следующий URL-адрес изображения образца:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Параметры запроса, заголовки и поле "Request body" (Текст запроса) в консоли "Job - Create" (Задание — создание)](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Отправка запроса

Выберите **Отправить**. Если операция успешно, **статус ответа,** `200 OK`и окно **содержимого ответа** отображает идентификатор для задания. Скопируйте этот идентификатор, чтобы использовать на следующих шагах.

![Идентификатор проверки в поле "Response content" (Содержимое ответа) в консоли "Review - Create" (Проверка — создание)](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Получение состояния задания

Чтобы получить статус и сведения о запущенной или завершенной работе, перейдите на справочную страницу [Job - Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API и выберите кнопку для вашего региона (региона, в котором управляется ключ).

![Работа - Получить выбор региона](images/test-drive-region.png)

Введите параметры вызова REST, как в вышеуказанном разделе. Для этого шага **JobId** — это уникальная строка ID, которую вы получили при создании задания. Выберите **Отправить**. Если операция выполняется успешно, `200 OK` **статус "Ответ"** отображает **задания** в формате JSON, например:

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

![Работа - Получить ответ на вызов REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Изучите новый обзор (ы)

Если работа с контентом привела к созданию обзора, вы можете просмотреть его в [инструменте «Обзор».](https://contentmoderator.cognitive.microsoft.com) Выберите **Обзор** > **изображения**/**Текстовое**/**видео** (в зависимости от того, что содержание вы использовали). Содержимое должно отображаться, готовое к человеческому обзору. После того, как модератор-человек просматривает автоматически назначенные теги и данные прогнозирования и отправляет окончательное решение о модерации, API заданий отправляет всю эту информацию в указанную конечную точку обратного вызова.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как создавать и задания модерации содержимого с помощью REST API. Затем интегрируйте задания в сценарий сквозной модерации, например учебник по [модерации электронной коммерции.](./ecommerce-retail-catalog-moderation.md)
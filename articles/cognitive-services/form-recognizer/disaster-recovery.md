---
title: Руководство по аварийному восстановлению для распознавателя форм Azure
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать API копирования модели для резервного копирования ресурсов распознавателя форм.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221848"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Резервное копирование и восстановление моделей распознавателя форм

При создании ресурса распознавателя форм в портал Azure указывается регион. После этого ваш ресурс и все его операции остаются связанными с этим конкретным регионом сервера Azure. Это случается редко, но не возможно, чтобы столкнуться с сетевой проблемой, которая достигает всего региона. Если решение всегда должно быть доступно, необходимо разработать его для отработки отказа в другой регион или разделить рабочую нагрузку между двумя или более регионами. Для обоих подходов требуется по крайней мере два ресурса распознавателя форм в разных регионах и возможность синхронизации [пользовательских моделей](./quickstarts/curl-train-extract.md) в регионах.

API копирования включает этот сценарий, позволяя копировать пользовательские модели из одной учетной записи распознавателя форм или в другие, которые могут существовать в любом поддерживаемом географическом регионе. В этом руководство показано, как использовать копирование REST API с помощью фигурного копирования. Можно также использовать службу HTTP-запросов, например POST, чтобы выдать запросы.

## <a name="business-scenarios"></a>Бизнес-сценарии

Если приложение или бизнес зависит от использования настраиваемой модели распознавателя форм, рекомендуется скопировать модель в другую учетную запись распознавателя форм в другом регионе. Если происходит региональный сбой, можно получить доступ к модели в регионе, в котором она была скопирована.

##  <a name="prerequisites"></a>Предварительные требования

1. Ресурсы Azure для распознавания двух форм в разных регионах Azure. Если у вас их нет, перейдите к портал Azure и <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" Создайте новый ресурс распознавателя форм, чтобы " target="_blank"> создать новый ресурс распознавателя форм <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. Ключ подписки, URL-адрес конечной точки и идентификатор подписки ресурса распознавателя форм. Эти значения можно найти на вкладке **Обзор** ресурса на портал Azure.


## <a name="copy-api-overview"></a>Обзор API копирования

Процесс копирования пользовательской модели состоит из следующих этапов.

1. Сначала вы выдаете запрос на копирование авторизации в целевой ресурс, то &mdash; есть ресурс, который получит скопированную модель. Вы получаете URL-адрес вновь созданной целевой модели, которая получит скопированные данные.
1. Затем отправьте запрос на копирование в исходный ресурс, который &mdash; содержит ресурс, содержащий копируемую модель. Вы получите URL-адрес, который можно запросить для отслеживания хода выполнения операции.
1. Учетные данные исходного ресурса будут использоваться для запроса URL-адреса хода выполнения до тех пор, пока операция не завершится успешно. Можно также запросить новый идентификатор модели в целевом ресурсе, чтобы получить состояние новой модели.

## <a name="generate-copy-authorization-request"></a>Создать запрос на авторизацию копирования

Следующий HTTP-запрос получает разрешение Copy на копирование из целевого ресурса. Необходимо ввести конечную точку и ключ целевого ресурса в качестве заголовков.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Вы получите `201\Created` ответ со `modelId` значением в тексте. Эта строка представляет собой идентификатор вновь созданной (пустой) модели. Объект `accessToken` необходим для того, чтобы API скопировал данные в этот ресурс, а `expirationDateTimeTicks` значение — истечение срока действия маркера. Сохраните все три значения в безопасном месте.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Запуск операции копирования

Следующий HTTP-запрос запускает операцию копирования в исходном ресурсе. Необходимо ввести конечную точку и ключ исходного ресурса в качестве заголовков. Обратите внимание, что URL-адрес запроса содержит идентификатор модели исходной модели, которую необходимо скопировать.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Текст запроса должен иметь следующий формат. Необходимо ввести идентификатор ресурса и имя региона целевого ресурса. Вам также потребуется идентификатор модели, маркер доступа и значение срока действия, скопированные на предыдущем шаге.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

Вы получите `202\Accepted` ответ с заголовком Operation-Location. Это значение представляет собой URL-адрес, который будет использоваться для отслеживания хода выполнения операции. Скопируйте его во временную папку для следующего шага.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>Отслеживать ход копирования

Отследите ход выполнения, запросив API **получения результатов модели копирования** для конечной точки исходного ресурса.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Ваш ответ будет зависеть от состояния операции. Найдите `"status"` поле в тексте JSON. Если вы автоматизируем этот вызов API в скрипте, мы рекомендуем запросить операцию каждую секунду.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>Используемых Отслеживание идентификатора целевой модели 

Можно также использовать API **получения настраиваемой модели** для наблюдения за состоянием операции путем запроса к целевой модели. Вызовите этот API, используя идентификатор целевой модели, скопированный на первом шаге.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

В тексте ответа вы увидите сведения о модели. Проверьте `"status"` состояние модели в поле.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>Фигурный образец кода

В следующих фрагментах кода используется фигурная скобка для выполнения вызовов API, описанных в приведенных выше шагах. Вам по-прежнему придется заполнять идентификаторы моделей и сведения о подписке, относящиеся к собственным ресурсам.

### <a name="generate-copy-authorization-request"></a>Создать запрос на авторизацию копирования

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Запуск операции копирования

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Отслеживать ход копирования

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководство вы узнали, как использовать API копирования для резервного копирования пользовательских моделей в ресурс распознаватель вторичных форм. Далее изучите справочную документацию по API, чтобы узнать, что еще можно сделать с помощью распознавателя форм.
* [Справочная документация по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
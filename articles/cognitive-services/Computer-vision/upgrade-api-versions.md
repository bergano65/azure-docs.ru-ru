---
title: Обновление до API чтения для Компьютерного зрения версии 3.0
titleSuffix: Azure Cognitive Services
description: Узнайте, как обновить API чтения для Компьютерного зрения с версии 2.0 или 2.1 до версии 3.0.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: c3394156b073df54d6582dc43571137b21df29cd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968945"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Обновление с чтения версии 2.x до версии 3.x

В этом руководстве показано, как обновить имеющийся контейнер или код облачного API с чтения версии 2.x до версии 3.0 и 3.1 (предварительная версия).

## <a name="determine-your-api-path"></a>Определение пути к API
Используйте следующую таблицу, чтобы определить **строку версии** в пути API на основе чтения версии 3.x, на которую выполняется переход.

|Тип продукта| Версия | Строка версии в пути API 3.x |
|:-----|:----|:----|
|Служба | Чтение 3.0 | **Версия 3.0** |
|Контейнер | Чтение 3.0 (предварительная версия) | **Версия 3.0** |
|Служба или контейнер | Чтение 3.1 (предварительная версия) | **v3.1-preview.2** |

Далее используйте следующие разделы, чтобы сократить количество операций и заменить **строку версии** в пути API значением из таблицы. Например, для **чтения версии 3.1 (предварительная версия)** облачной версии и версии контейнера измените путь API на **https://{конечная_точка}/vision/v3.1-preview.2/read/analyze[?language]** .

## <a name="servicecontainer"></a>Служба или контейнер

### `Batch Read File`

|Чтение 2.x |Чтение 3.x  |
|----------|-----------|
|https://{конечная_точка}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{конечная_точка}/vision/<**version string**>/read/analyze[?language]|
    
Появился новый необязательный параметр _language_. Если язык документа неизвестен или языков может быть несколько, не используйте его. 

### `Get Read Results`

|Чтение 2.x |Чтение 3.x  |
|----------|-----------|
|https://{конечная_точка}/vision/**v2.0/read/operations**/{идентификатор_операции}     |https://{конечная_точка}/vision/<**version string**>/read/analyzeResults/{идентификатор_операции}|

### <a name="get-read-operation-result-status-flag"></a>Флаг состояния `Get Read Operation Result`

Когда вызов к `Get Read Operation Result` выполняется успешно, он возвращает в тексте JSON поле строки состояния.
 
|Чтение 2.x |Чтение 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Ответ API (JSON) 

Обратите внимание, что с форматом JSON связаны следующие изменения:
* В версии 2.x `Get Read Operation Result` будет возвращать JSON распознавания текста, если состояние имеет значение `Succeeded"`. В версии 3.0 это поле `succeeded`.
* Чтобы получить корень адреса для массива страниц, измените иерархию JSON с `recognitionResults` на `analyzeResult`/`readResults`. Иерархия строк и слов на странице JSON остается неизменной, поэтому код изменять не нужно.
* Угол направления страницы `clockwiseOrientation` был переименован в `angle`, а диапазон теперь составляет от -180 до 180 градусов вместо от 0 до 360 градусов. В зависимости от конкретного кода изменения могут потребоваться или не потребоваться, так как большинство математических функций нормально работают с любым из этих диапазонов.

В API версии 3.0 также представлены следующие улучшения, которые можно использовать дополнительно.
* Добавлены `createdDateTime` и `lastUpdatedDateTime`, чтобы отслеживать продолжительность обработки. Дополнительные сведения см. в документации. 
* `version` определяет версию API, которая использовалась для создания результатов.
* Добавлен параметр `confidence` для каждого слова. Это значение калибруется так, чтобы значение 0,95 означало вероятность правильного распознавания, равную 95 %. Этот показатель достоверности можно использовать для выбора текста, отправляемого на просмотр человеку. 
    
    
В версии 2.x используется следующий формат выходных данных: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
В версии 3.0 он был скорректирован:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Только служба

### `Recognize Text`
Операция `Recognize Text` предоставлялась в режиме *предварительной версии*, и теперь считается *устаревшей во всех версиях API Компьютерного зрения*. Вам необходимо перейти с `Recognize Text` на `Read` (версия 3.0) или `Batch Read File` (версии 2.0 и 2.1). В `Read` версии 3.0 включены новые и более эффективные модели распознавания текста и дополнительные функции, поэтому мы рекомендуем использовать именно эту версию. Для обновления с `Recognize Text` до `Read` сделайте следующее:

|Распознавание текста 2.x |Чтение 3.x  |
|----------|-----------|
|https://{конечная_точка}/vision/**v2.0/recognizeText[?mode]**|https://{конечная_точка}/vision/<**version string**>/read/analyze[?language]|
    
Параметр _mode_ не поддерживается в `Read`. Рукописный и печатный текст будут поддерживаться автоматически.
    
В версии 3.0 появился новый необязательный параметр _language_. Если язык документа неизвестен или языков может быть несколько, не используйте его. 

### `Get Recognize Text Operation Result`

|Распознавание текста 2.x |Чтение 3.x  |
|----------|-----------|
|https://{конечная_точка}/vision/**v2.0/textOperations/** {идентификатор_операции}|https://{конечная_точка}/vision/<**version string**>/read/analyzeResults/{идентификатор_операции}|

### <a name="get-recognize-text-operation-result-status-flags"></a>Флаг состояния `Get Recognize Text Operation Result`
Когда вызов к `Get Recognize Text Operation Result` выполняется успешно, он возвращает в тексте JSON поле строки состояния. 
 
|Распознавание текста 2.x |Чтение 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Ответ API (JSON)

Обратите внимание, что с форматом JSON связаны следующие изменения:    
* В версии 2.x `Get Read Operation Result` будет возвращать JSON распознавания текста, если состояние имеет значение `Succeeded`. В версии 3.x это поле `succeeded`.
* Чтобы получить корень адреса для массива страниц, измените иерархию JSON с `recognitionResult` на `analyzeResult`/`readResults`. Иерархия строк и слов на странице JSON остается неизменной, поэтому код изменять не нужно.

В API версии 3.0 также представлены следующие улучшения, которые можно использовать дополнительно. Дополнительные сведения см. в справочнике по API.
* Добавлены `createdDateTime` и `lastUpdatedDateTime`, чтобы отслеживать продолжительность обработки. Дополнительные сведения см. в документации. 
* `version` определяет версию API, которая использовалась для создания результатов.
* Добавлен параметр `confidence` для каждого слова. Это значение калибруется так, чтобы значение 0,95 означало вероятность правильного распознавания, равную 95 %. Этот показатель достоверности можно использовать для выбора текста, отправляемого на просмотр человеку. 
* Общая ориентация текста `angle` в направлении по часовой стрелке, в диапазоне от -180 до 180 градусов.
* `width` и `"height"` предоставляют размеры документа, а `"unit"` — единицы измерения для этих размеров (пиксели или дюймы, в зависимости от типа документа).
* Поддерживаются многостраничные документы `page`.
* Язык ввода `language` для документа (извлекается из необязательного параметра _language_).


В версии 2.x используется следующий формат выходных данных: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
В версии 3.x он был скорректирован:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Только контейнер

### `Synchronous Read`

|Чтение 2.0 |Чтение 3.x  |
|----------|-----------|
|https://{конечная_точка}/vision/**v2.0/read/core/Analyze**     |https://{конечная_точка}/vision/<**version string**>/read/syncAnalyze[?language]|

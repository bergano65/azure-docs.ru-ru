---
title: Анализ видео в реальном времени с помощью выбранного искусственного интеллекта в Azure
description: В этой статье вы узнаете, как создать модуль IoT Edge, который можно интегрировать с помощью функции Live Video Analytics на IoT Edge, чтобы проанализировать видео в реальном времени, используя модель компьютерного зрения по своему усмотрению.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260647"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Анализ видеотрансляции с помощью предпочитаемого средства ИИ

В этой статье вы узнаете, как создать модуль IoT Edge, который можно интегрировать с помощью функции Live Video Analytics на IoT Edge, чтобы проанализировать видео в реальном времени, используя модель компьютерного зрения по своему усмотрению. 

## <a name="pre-reading"></a>Предварительное чтение

[Понятие графа мультимедиа](media-graph-concept.md)

## <a name="media-graph-extension"></a>Расширение графа мультимедиа

Функция Live Video Analytics на IoT Edge определяет модель расширяемости, которая позволяет расширять возможности обработки графа мультимедиа в собственные компоненты Media Analytics с помощью расширения Graph. Компонент аналитики может использовать традиционные методы обработки изображений или модели ИСКУССТВЕНного зрения компьютерных концепций. Расширения графа включены, включая узел [обработчика расширений HTTP](media-graph-concept.md#http-extension-processor) в графе мультимедиа. Обработчик расширений HTTP может передавать видеоматериалы в конечную точку HTTP, указанную вами, и действует как интерфейс компонента через это. Подключение можно установить к локальной или удаленной конечной точке, и при необходимости его можно защитить с помощью проверки подлинности и TLS-шифрования. Кроме того, процессор позволяет дополнительно масштабировать и кодировать видеокадры до их ретрансляции.

Вы можете запустить выбранную модель вывода в любой доступной среде выполнения вывода, такой как ONNX, TensorFlow, PyTorch или другие, в собственном контейнере DOCKER. Вы также можете использовать язык программирования и библиотеки по своему усмотрению, чтобы предоставить возможность создания образа, хотя HTTP-сервер находится в вашем контейнере. Контейнер для получения данных должен быть развернут вместе с модулем Live Video Analytics, чтобы обеспечить лучшую производительность и затем вызываться с помощью обработчика расширений HTTP, включенного в топологию графа.
Кроме того, частоту вызовов к пользовательскому модулю можно регулировать, при необходимости добавляя [обработчик детектора движения](media-graph-concept.md#motion-detection-processor) и переходящий [обработчик фильтров кадров](media-graph-concept.md#frame-rate-filter-processor) к обработчику расширений HTTP.

На схеме ниже показан поток данных высокого уровня.

![Пограничное устройство](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Это позволяет анализировать видео с использованием моделей искусственного интеллекта по своему усмотрению в соответствии с уникальными потребностями бизнеса. Модели искусственного интеллекта могут быть из сообщества с открытым исходным кодом, от специалистов по обработке данных или от специализированного поставщика искусственного интеллекта.

## <a name="media-graph-http-extension-contract-definitions"></a>Определения контрактов HTTP-расширения Media Graph

В этом разделе определяется контракт HTTP, определяющий поток данных.

### <a name="http-extensibility-protocol"></a>Протокол расширяемости HTTP  

Контракт HTTP определяется следующим образом:

* Модуль выступает в качестве HTTP-сервера.
* Интерактивная аналитика видео в модуле IoT Edge выступает в качестве клиента HTTP.

### <a name="request"></a>Запрос

Запросы от модуля Live Video Analytics к модулю будут выглядеть следующим образом:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Принять|Application/JSON,*/*|
|Авторизация| Basic, Digest, Bearer (с помощью поддержки пользовательских заголовков)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>Image/x-RAW|
|Content-Length|Длина текста в байтах|
|User-Agent|Службы мультимедиа Azure|
|Текст|Байт изображения, двоичная кодировка в одном из поддерживаемых типов содержимого.|

#### <a name="example"></a>Пример

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Ответ

Ответы из модуля в Live Video Analytics должны выглядеть следующим образом

|||
|---|---|
|Коды состояний|200 ОК — найдены результаты вывода<br/>204 нет содержимого — не найдено содержимое, найденное в AI<br/>400. неверный запрос — не ожидается<br/>500 Внутренняя ошибка сервера — не ожидается<br/>503 Server Busy-AMS будет отключаться на основе заголовка "Retry-After" или на основе заданного по умолчанию промежутка времени в заголовке регистра без предустановки.|
|Content-Type|приложение/json|
|Content-Length|    Длина текста в байтах|
|Текст|Объект JSON с одним свойством "вывод".|

#### <a name="example"></a>Пример

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Настоятельно рекомендуется возвратить ответы с помощью допустимых документов JSON после предварительно установленной схемы, определенной ниже. Это обеспечит лучшую совместимость с другими компонентами и возможными будущими возможностями, добавленными в модуль Live Video Analytics.

Если модуль возвращает ответ, где тип содержимого не является "Application/JSON", то в реальном времени будет кодироваться сообщение как базовое содержимое 64 и сериализовать его как непрозрачные полезные данные JSON.

Если модуль возвращает ответ с типом содержимого "Application/JSON", но схема JSON не соответствует [схеме метаданных определения, описанной ниже](#inference-metadata-schema), полезные данные сообщения будут перенаправлены через конвейер, но взаимодействие будет сокращено.

> [!NOTE]
> Если модуль не создает никаких результатов, он должен вернуть код состояния HTTP 204 (без содержимого) с пустым текстом ответа. Live Video Analytics поймет это как пустой результат и не будет пересылать событие по всему конвейеру.

### <a name="inference-metadata-schema"></a>Схема метаданных вывода

Каждый объект вывода соответствует схеме надмножества:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Контракты данных — Иерархия классов

![Контракты данных — Иерархия классов](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Примеры  

В приведенном ниже примере содержится одно событие со всеми поддерживаемыми типами вывода:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Примеры модулей расширения HTTP

Несколько примеров модулей расширения HTTP можно найти в [репозитории GitHub в Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). В одном из этих [примеров анализа видео](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) показано, как использовать [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) Model для создания модуля IOT Edge для обнаружения объектов. Такой же подход можно использовать для создания собственного модуля с моделью AI по своему усмотрению.

## <a name="next-steps"></a>Дальнейшие шаги

[Устранение проблем](troubleshoot-how-to.md)

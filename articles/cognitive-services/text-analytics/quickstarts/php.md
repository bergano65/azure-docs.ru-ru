---
title: Краткое руководство. Вызов API анализа текста с использованием PHP
titleSuffix: Azure Cognitive Services
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API Анализа текста в Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 3b95109ea3389cc8ee7e748e06265870d5f903a4
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284757"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Краткое руководство. Использование PHP для вызова API анализа текста Cognitive Services
<a name="HOLTop"></a>

В этой статье содержатся сведения о том, как [распознавать язык](#Detect), [анализировать тональность](#SentimentAnalysis), [извлекать ключевые фразы](#KeyPhraseExtraction) и [идентифицировать связанные сущности](#Entities), используя  [API анализа текста](//go.microsoft.com/fwlink/?LinkID=759711) и PHP.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Определение языка

API распознавания языка определяет язык текстового документа, используя [метод определения языка](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект PHP в используемой вами интегрированной среде разработки.
1. Добавьте указанный ниже код.
1. Запустите программу.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Ответ функции распознавания языка**

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Анализ тональности

API анализа тональности определяет тональность набора текстовых записей с помощью [метода определения тональности](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). В следующем примере выполняется оценка двух документов — на английском и на испанском языках.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект PHP в используемой вами интегрированной среде разработки.
1. Добавьте указанный ниже код.
1. Запустите программу.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/sentiment';

function GetSentiment ($host, $path, $key, $data) {
    // Make sure all text is UTF-8 encoded.
    foreach ($data as &$item) {
        foreach ($item as $ignore => &$value) {
            $value['text'] = utf8_encode($value['text']);
        }
    }

    $data = json_encode ($data);

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Ответ функции анализа тональности**

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Извлечение ключевых фраз

API извлечения ключевых фраз извлекает ключевые фразы из текстового документа с помощью [метода ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Следующий пример извлекает ключевые фразы в документах на английском и испанском языках.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект PHP в используемой вами интегрированной среде разработки.
1. Добавьте указанный ниже код.
1. Запустите программу.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Ответ функции извлечения ключевых фраз**

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Определение сущностей 

API сущностей определяет известные сущности в текстовом документе, используя [метод Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). Следующий пример определяет сущности в документах на английском языке.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект PHP в используемой вами интегрированной среде разработки.
1. Добавьте указанный ниже код.
1. Запустите программу.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . Length($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Ответ функции извлечения сущностей**

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)

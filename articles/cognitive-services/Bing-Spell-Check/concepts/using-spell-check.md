---
title: Использование API "Проверка орфографии Bing"
titleSuffix: Azure Cognitive Services
description: В этой статье описаны режимы проверки орфографии Bing, параметры и другие сведения, относящиеся к API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: c5c9ad8be8bd4cd834b01a0c67e0bbc81b8cdd4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881889"
---
# <a name="using-the-bing-spell-check-api"></a>Использование API "Проверка орфографии Bing"

В этой статье показано, как использовать API "Проверка орфографии Bing" для контекстной проверки орфографии и грамматики. Большинство средств проверки орфографии выполняют проверку с использованием наборов правил на основе словаря, а в Bing используется машинное обучение и статистический машинный перевод для точных исправлений с учетом контекста. 

## <a name="spell-check-modes"></a>Режимы проверки орфографии

API поддерживает два режима проверки орфографии: `Proof` и `Spell`.  Примеры см. [здесь](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Режим Proof для документов 

`Proof` является режимом по умолчанию. Режим проверки орфографии `Proof` предоставляет наиболее комплексные проверки (проверка капитализации, базовой пунктуации) и другие возможности, которые помогают в создании документа. Но он доступен только для языковых стандартов en-US (английский — США), es-ES (Испания) и pt-BR (Португалия). (Примечание. Для Испании и Португалии доступна только бета-версия.) Для других языковых стандартов (рынков) установите для параметра запроса mode значение Spell. 

> [!NOTE]
> Если длина текста запроса превышает 4096 символов, он будет усечен до этого количества, а затем обработан. 

### <a name="spell----for-web-searchesqueries"></a>Spell — для веб-поиска или запросов

`Spell` отличается более строгими требованиями и возвращает более точные результаты поиска. Режим `Spell` определяет большинство орфографических ошибок, но не находит некоторые грамматические ошибки, которые улавливает `Proof`, например регистр букв и повторяющиеся слова.

> [!NOTE]
> * Ниже указана максимальная поддерживаемая длина запроса. Если длина запроса превышает максимально допустимую, запрос и его результаты не изменяются.
>    * 130 символов для следующих кодов языков: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 символов для всех остальных кодов языков.
> * Режим проверки орфографии не поддерживает символы квадратных скобок (`[` и `]`) в запросах. Их использование может привести к несогласованным результатам. Мы рекомендуем удалить их из запросов в режиме проверки орфографии.

## <a name="market-setting"></a>Настройка рынка

В запросе[код рынка](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) необходимо указать с помощью параметра `mkt`. Иначе, API будет использовать рынок по умолчанию в зависимости от IP-адреса, с которого отправлен запрос.


## <a name="http-post-and-get-support"></a>Поддержка HTTP запросов POST и GET

Этот API поддерживает либо HTTP-запрос POST, либо HTTP-запрос GET. Используемый запрос зависит от длины текста, который нужно проверить. Если строки всегда меньше 1500 символов, можно использовать запрос GET. Но если вам нужно поддерживать строки размером до 10 000 символов, используйте запрос POST. Текстовая строка может содержать любой допустимый символ в формате UTF-8.

В следующем примере показан запрос POST для проверки орфографии и грамматики текстовой строки. Пример включает в себя параметр запроса [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) для полноты информации (его можно опустить, так как параметр `mode` по умолчанию имеет значение Proof). Параметр запроса [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) содержит строку, которую нужно проверить.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

При использовании HTTP-запроса GET следует включить параметр запроса `text` в строку запроса с URL-адресом.
  
Ниже показан ответ на предыдущий запрос. Ответ будет содержать объект [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
В поле [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) перечислены орфографические и грамматические ошибки, найденные API в строке [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). Поле `token` содержит слова для замены. Используйте смещение, отсчитываемое с нуля, в поле `offset`, чтобы найти токен в строке `text`. Необходимо заменить слово в данном расположении на слово из поля `suggestion`. 

Если в поле `type` указано значение RepeatedToken, вам все еще нужно заменить токен на `suggestion`, но, скорее всего, потребуется удалить конечный пробел.

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Следующие шаги

- [Что такое API проверки орфографии Bing?](../overview.md)
- [Справочник по API Проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)

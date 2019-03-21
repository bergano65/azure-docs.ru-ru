---
title: Предложение условий поиска с помощью API автозаполнения Bing
titlesuffix: Azure Cognitive Services
description: Узнайте, как использовать API автозаполнения Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 293dcaadfc20116455983b3fc0069f9e9df3f843
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010554"
---
# <a name="suggesting-query-terms"></a>Предложение терминов запроса

API автозаполнения Bing обычно вызывается всякий раз, когда пользователь вводит новый символ в поле поиска вашего приложения. Полнота строки запроса влияет на релевантность предложений, возвращаемых API-интерфейсом. Чем ближе строка запроса к завершению, тем точнее будет список предложений для этого запроса. Например, возвращаемые API-интерфейсом предложения при вводе `s`, скорее всего, будут менее значимыми, чем результаты для фразы `sailing dinghies`.

## <a name="example-request"></a>Пример запроса

В следующем примере показано, как отправить запрос, который возвращает предлагаемые строки запроса для слова *sail*. Не забудьте закодировать в URL-адресе частичный запрос условия пользователя, когда устанавливаете параметр запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Например, если пользователь вводит *sailing les*, установите параметр `q` в `sailing+les` или `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Этот ответ содержит список объектов [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) с предлагаемыми условиями запроса.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Использование предлагаемых условий запроса

Каждое из предложений содержит поля `displayText`, `query` и `url`. Поле `displayText` содержит предлагаемые запросы, которые вы можете поместить в раскрывающийся список для поля поиска. Должны отображаться все предоставленные предложения и строго в указанном порядке.

В примере ниже показан раскрывающийся список поля поиска с предложениями условий запроса из API "Автозаполнение Bing".

![Автозаполнение раскрывающегося списка поля поиска](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Если пользователь выбирает из раскрывающегося списка предложенный запрос, используйте условие запроса из поля `query`, чтобы вызвать раздел [Документация по API Bing для поиска в Интернете](../../bing-web-search/search-the-web.md) и отобразить полученные результаты. Или вы можете использовать URL-адрес из поля `url`, чтобы переадресовать пользователя на страницу результатов поиска Bing.

## <a name="next-steps"></a>Дополнительная информация

* [Что такое API автозаполнения Bing?](../get-suggested-search-terms.md)
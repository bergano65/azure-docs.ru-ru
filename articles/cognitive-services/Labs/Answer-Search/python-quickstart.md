---
title: Краткое руководство. Служба поиска ответов в проекте, Python
titlesuffix: Azure Cognitive Services
description: Пример на Python для быстрого начала работы с API поиска ответов в проектах.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: c35bf9649a0a22f3488c45d1f4f8729e211e0ddb
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707079"
---
# <a name="quickstart-project-answer-search-with-python"></a>Краткое руководство по работе с API поиска ответов в проектах с использованием Python

В следующем примере для Python создается и отправляется запрос для получения сведений о "Rock of Gibraltar" (Гибралтарская скала).

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search).

В этом примере используется Python 3.6.4.

## <a name="code-scenario"></a>Сценарий кода 

Следующий код создает представление для предварительного просмотра URL-адреса.
Он реализуется с помощью следующих действий:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Укажите URL-адрес запроса для предварительного просмотра и добавьте параметр запроса.  
3. Задайте параметр запроса.
4. Определите функцию поиска, которая создает запрос и добавляет заголовок *Ocp-Apim-Subscription-Key*.
5. Задайте заголовок *Ocp-Apim-Subscription-Key*. 
6. Выполните подключение и отправьте запрос.
7. Выведите на экран результаты JSON.

Полный код этого примера выглядит так:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```
## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](c-sharp-quickstart.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство — Node](node-quickstart.md)

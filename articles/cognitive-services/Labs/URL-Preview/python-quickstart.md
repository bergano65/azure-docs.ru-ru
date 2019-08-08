---
title: Краткое руководство. Служба предварительного просмотра URL-адресов в проекте, Python
titlesuffix: Azure Cognitive Services
description: Пример скрипта для быстрого начала работы со службой предварительного просмотра URL-адресов в проектах с использованием Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e9b145b0786b2ab59552e5dfda6f0db63677443e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706882"
---
# <a name="quickstart-url-preview-with-python"></a>Краткое руководство. Предварительный просмотр URL-адресов с использованием Python

Следующий пример Python создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search).

В этом примере используется Python 3.6.

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
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](csharp.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство по использованию службы предварительного просмотра URL-адресов для Node](node-quickstart.md)

---
title: Краткое руководство по использованию Python для службы предварительного просмотра URL-адресов в проектах
titlesuffix: Azure Cognitive Services
description: Пример скрипта для быстрого начала работы со службой предварительного просмотра URL-адресов в проектах с использованием Python.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: ecf98803e5313812a074442b70bb376ab67e7366
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470002"
---
# <a name="quickstart-url-preview-with-python"></a>Краткое руководство по использованию Python для службы предварительного просмотра URL-адресов

Следующий пример Python создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

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

````
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
````
## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](csharp.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство по использованию службы предварительного просмотра URL-адресов для Node](node-quickstart.md)
---
title: Краткое руководство. Публикация базы знаний — REST, Python — QnA Maker
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается процесс публикации с использованием REST для базы знаний, которая передает последнюю версию протестированной базы знаний в выделенный индекс службы "Поиск Azure", представляющий опубликованную базу знаний. Кроме того, создается конечная точка, которая может быть вызвана в приложении или чат-боте.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e58b344102eb900ffe41bb90e541258eb3b59286
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646820"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Краткое руководство. Публикация базы знаний в QnA Maker с помощью Python

В этом кратком руководстве описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

1. Создайте проект Python в используемой вами интегрированной среде разработки.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

## <a name="the-publish-a-knowledge-base-response"></a>Ответ при успешной публикации базы знаний

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))

[Общие сведения о QnA Maker](../Overview/overview.md)
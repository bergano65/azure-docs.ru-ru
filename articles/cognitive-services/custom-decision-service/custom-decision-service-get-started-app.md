---
title: Вызов API из приложения — пользовательская служба принятия решений
titlesuffix: Azure Cognitive Services
description: Как вызывать API пользовательской службы принятия решений из приложения для смартфона.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: fed45ec10eca26c58bc3b3c70991c1f7f468e393
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219419"
---
# <a name="call-api-from-an-app"></a>Вызов API из приложения

API-интерфейсы Пользовательской службы принятия решений Azure можно вызывать из приложения для смартфона. В этой статье объясняется, как начать работу с некоторыми основными параметрами.

Не забудьте сначала [зарегистрировать приложение](custom-decision-service-get-started-register.md).

Из приложения для смартфона можно выполнить два вызова Пользовательской службы принятия решений: вызов API ранжирования для получения ранжированного списка содержимого и вызов API вознаграждения для сообщения о вознаграждении. Вот примеры вызовов в [cURL](https://en.wikipedia.org/wiki/CURL).

Дополнительные сведения см. в справочнике по [API](custom-decision-service-api-reference.md).

Начните с вызова API ранжирования. Создайте файл `<request.json>`, у которого есть идентификатор набора действий. Этот идентификатор представляет собой имя соответствующего веб-канала RSS или Atom, введенное на портале:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Можно указать несколько наборов действий следующим образом:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Затем этот JSON-файл передается в составе запроса ранжирования:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Здесь `<appId>` — имя приложения, зарегистрированного на портале. Вы должны получить упорядоченный набор элементов содержимого, который сможете преобразовать для просмотра в приложении. Пример возвращаемых данных:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Первая часть возвращаемых данных содержит список упорядоченных действий, заданный идентификаторами действий. Для статьи идентификатором действия является URL-адрес. Общий запрос также имеет уникальный идентификатор `<eventId>`, созданный системой.

Позже можно указать, наблюдался ли щелчок по первому элементу содержимого из этого события, то есть `<actionId3>`. Затем можно сообщить о вознаграждении по этому `<eventId>` в Пользовательскую службу принятия решений через API вознаграждения с помощью другого запроса:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Наконец, необходимо указать API набора действий, который возвращает список статей (действий), рассматриваемых Пользовательской службой принятия решений. Реализуйте этот API в виде RSS-канала, как показано ниже:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Здесь каждый элемент `<item>` верхнего уровня описывает одну статью. Элемент `<link>` является обязательным и используется Пользовательской службой принятия решений как идентификатор действия. Укажите `<date>` (в стандартном формате RSS) при наличии более чем 15 статей. Используются 15 последних статей. Элемент `<title>` необязателен и используется для создания связанных с текстом признаков статьи.

## <a name="next-steps"></a>Дополнительная информация

* Изучите [руководство](custom-decision-service-tutorial-news.md) с более подробным примером.
* Обратитесь к справочнику по [API](custom-decision-service-api-reference.md), чтобы получить дополнительные сведения о предоставляемых возможностях.

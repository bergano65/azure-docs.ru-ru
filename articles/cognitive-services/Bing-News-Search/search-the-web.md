---
title: Общие сведения об API Bing для поиска новостей
titlesuffix: Azure Cognitive Services
description: Узнайте, как использовать API Bing для поиска актуальных новостей в Интернете, включая заголовки в нескольких категориях и популярные темы.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 32c18508d07fc911366ffc77ebe347efd3c1b6fa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261457"
---
# <a name="what-is-the-bing-news-search-api"></a>Общие сведения об API Bing для поиска новостей

API Bing для поиска новостей позволяет легко интегрировать возможности когнитивного поиска новости Bing в приложениях. Интерфейс API предоставляет аналогичное взаимодействие для [новостей Bing](https://www.bing.com/news), позволяя отправлять поисковые запросы и получать соответствующие статьи.

Имейте в виду, что API Bing для поиска новостей предоставляет только результаты поиска новостей. Используйте [API Bing для поиска в Интернете](../bing-web-search/search-the-web.md), [API для поиска видео](../bing-video-search/search-the-web.md) и [API Bing для поиска изображений](../bing-image-search/overview.md) для других типов веб-содержимого.

## <a name="bing-news-search-api-features"></a>Возможности API Bing для поиска новостей

API Bing для поиска новостей в основном находит и возвращает соответствующие статьи, а также предоставляет несколько функций для интеллектуального и тематического поиска новостей в Интернете.

|Функция  |ОПИСАНИЕ  |
|---------|---------|
|[Предложение и использование условий поиска](concepts/search-for-news.md#suggest-and-use-search-terms)     | Улучшите возможности поиска, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md) для отображения предлагаемых условий поиска по мере их ввода.         |
|[Получение общих новостей](concepts/search-for-news.md#get-general-news)     | Ищите новости, отправляя поисковый запрос в API Bing для поиска новостей и получая список соответствующих новостных статей.           |
|[Главные новости за сегодня](concepts/search-for-news.md#get-todays-top-news)      | Получите главные новости дня по всем категориям.       |
|[Новости по категории](concepts/search-for-news.md)     | Ищите новости в определенной категории.        | 
|[Сводка новостей](concepts/search-for-news.md)     | Ищите главные заголовки по всем категориям.         |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска новостей является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализа JSON. Вы можете использовать службу с помощью REST API или пакета SDK.

1. Создайте учетную запись API Cognitive Services с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Отправьте запрос к API с допустимым поисковым запросом.

3. Обработайте ответ API путем анализа возвращенного сообщения JSON.

## <a name="next-steps"></a>Дополнительная информация

Сначала попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) API Bing для поиска новостей. В этой демоверсии показано, как можно быстро настроить поисковый запрос и найти новости в Интернете.

Чтобы быстро приступить к работе с первым запросом API, изучите статью [Краткое руководство. Поиск новостей с помощью C# и REST API Bing для поиска новостей](quickstart.md) или [Краткое руководство. Поиск новостей с помощью пакета SDK Поиска новостей Bing для C#](sdk.md).

## <a name="see-also"></a>См. также

* В [этой статье](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) содержатся определения и сведения о конечных точках, заголовках, ответах API и параметрах запроса, которые вы можете использовать для запроса результатов поиска на основе изображения.

* В статье [Требования к использованию и отображению API поиска Bing](./useanddisplayrequirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.

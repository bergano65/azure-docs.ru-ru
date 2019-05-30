---
title: Что такое API Поиска видео Bing
titlesuffix: Azure Cognitive Services
description: Узнайте, как искать видео в Интернете с помощью API Bing для поиска видео.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: f56893f830720c57c66eb4c17bb2771efbb73f6f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "62124648"
---
# <a name="what-is-the-bing-video-search-api"></a>Что такое API Поиска видео Bing

API Bing для поиска видео позволяет легко добавлять возможности поиска видео в службы и приложения. Отправляя пользовательские поисковые запросы с помощью API, можете получить и отобразить соответствующие и высококачественные видео, которые аналогичны [Видео Bing](https://www.bing.com/video). Используйте этот API для результатов поиска, которые содержат только видео. [API Bing для поиска видео](../bing-web-search/search-the-web.md) может возвращать другие типы веб-содержимого, включая веб-страницы, видео, новости и изображения.

## <a name="bing-video-search-api-features"></a>Функции API Bing для поиска видео

| Функция                                                                                                                                                                                 | ОПИСАНИЕ                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Предложение условий поиска в режиме реального времени](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Улучшите работу приложения, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), отображающий предлагаемые условия поиска по мере их ввода. |
| [Фильтрация и ограничение результатов поиска видео](concepts/get-videos.md#filtering-videos)                      | Отфильтруйте возвращаемые видео путем изменения параметров запроса.                                                                                                       |
| [Обрезка, изменения размера и отображение эскизов](resize-and-crop-thumbnails.md)                                                | Изменяйте и отображайте предварительные версии видео, возвращаемых API Bing для поиска видео.                                                                                      |
| [Получение видео, набирающих популярность](trending-videos.md) | Поиск видео, набирающих популярность, со всего мира.                                                                                                          |
| [Получение полезных сведений о видео](video-insights.md) | Настройте поиск популярных видео со всего мира.                                                                                                          |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска видео является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализ JSON. Вы можете использовать службу с помощью [REST API](csharp.md) или [пакета SDK](video-search-sdk-quickstart.md).

1. Создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Отправьте запрос к API с допустимым поисковым запросом.
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.


## <a name="next-steps"></a>Дополнительная информация

В этой интерактивной демоверсии [API Bing для поиска видео](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) показано, как можно быстро настроить поисковый запрос и найти видео в Интернете.

Когда будете готовы вызвать API, создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Используйте [это краткое руководство](csharp.md), чтобы быстро начать работу с первым запросом API.

## <a name="see-also"></a>См. также

* Страница [Video Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) (Справочник по API Bing для поиска видео версии 7) содержит список конечных точек, заголовков и параметров запроса, используемых для запроса результатов поиска.

* В статье [Требования к использованию и отображению API поиска Bing](./useanddisplayrequirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.
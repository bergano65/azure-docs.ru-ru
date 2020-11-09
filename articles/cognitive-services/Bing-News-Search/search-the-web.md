---
title: Общие сведения об API Поиска новостей Bing
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать API Bing для поиска актуальных новостей в Интернете, включая заголовки в нескольких категориях и популярные темы.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 9ba749d671e24e86e2cd0a299e98ba03e47cf354
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101686"
---
# <a name="what-is-the-bing-news-search-api"></a>Общие сведения об API Поиска новостей Bing

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](https://aka.ms/cogsvcs/bingmove).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](https://aka.ms/cogsvcs/bingmigration).

API Bing для поиска новостей позволяет легко интегрировать возможности когнитивного поиска новости Bing в приложениях. Интерфейс API предоставляет аналогичное взаимодействие для [новостей Bing](https://www.bing.com/news), позволяя отправлять поисковые запросы и получать соответствующие статьи.

Имейте в виду, что API Bing для поиска новостей предоставляет только результаты поиска новостей. Используйте [API Bing для поиска в Интернете](../bing-web-search/search-the-web.md), [API для поиска видео](../bing-video-search/search-the-web.md) и [API Bing для поиска изображений](../bing-image-search/overview.md) для других типов веб-содержимого.

## <a name="bing-news-search-api-features"></a>Возможности API Bing для поиска новостей

API Bing для поиска новостей в основном находит и возвращает соответствующие статьи, а также предоставляет несколько функций для интеллектуального и тематического поиска новостей в Интернете.

|Компонент  |Описание  |
|---------|---------|
|[Предложение и использование условий поиска](concepts/search-for-news.md#suggest-and-use-search-terms)     | Улучшите возможности поиска, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md) для отображения предлагаемых условий поиска по мере их ввода.         |
|[Получение общих новостей](concepts/search-for-news.md#get-general-news)     | Ищите новости, отправляя поисковый запрос в API Bing для поиска новостей и получая список соответствующих новостных статей.           |
|[Главные новости за сегодня](concepts/search-for-news.md#get-todays-top-news)      | Получите главные новости дня по всем категориям.       |
|[Новости по категории](concepts/search-for-news.md)     | Ищите новости в определенной категории.        | 
|[Сводка новостей](concepts/search-for-news.md)     | Ищите главные заголовки по всем категориям.         |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска новостей является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализа JSON. Вы можете использовать службу с помощью REST API или пакета SDK.

1. Создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/free/cognitive-services/).
2. Отправьте запрос к API с допустимым поисковым запросом.
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.

## <a name="next-steps"></a>Дальнейшие действия

Сначала попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) API Bing для поиска новостей. В этой демоверсии показано, как можно быстро настроить поисковый запрос и найти новости в Интернете.

Чтобы быстро приступить к работе с первым запросом API, изучите статью [Краткое руководство. Поиск новостей с помощью C# и REST API Bing для поиска новостей](quickstart.md) или [Краткое руководство. Поиск новостей с помощью пакета SDK Поиска новостей Bing для C#](sdk.md).

## <a name="see-also"></a>См. также раздел

* В [этой статье](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) содержатся определения и сведения о конечных точках, заголовках, ответах API и параметрах запроса, которые вы можете использовать для запроса результатов поиска на основе изображения.
* В статье [Требования к использованию и отображению API поиска Bing](./useanddisplayrequirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.
* Сведения о других доступных API "Поиск Bing" см. на [главной странице](../bing-web-search/search-the-web.md).
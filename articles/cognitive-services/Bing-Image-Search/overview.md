---
title: Что из себя представляет API Поиска изображений Bing
titleSuffix: Azure Cognitive Services
description: API Bing для поиска изображений позволяет использовать возможности когнитивного поиска изображений Bing в своем приложении. Отправляя пользовательские поисковые запросы с помощью API, можно получить и отобразить соответствующие и высококачественные изображения, которые аналогичны изображениям Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338237"
---
# <a name="what-is-the-bing-image-search-api"></a>Что из себя представляет API Поиска изображений Bing

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API Bing для поиска изображений позволяет использовать возможности поиска изображений Bing в своем приложении. Отправляя поисковые запросы в API, вы сможете получать высококачественные изображения, как и при использовании [bing.com/images](https://www.bing.com/images).

API-интерфейс Bing для поиска изображений предоставляет только результаты поиска изображений, но вы можете использовать вместе с ним (или отдельно) другие доступные [API-интерфейсы поиска Bing](../bing-web-search/bing-api-comparison.md) для поиска различных типов содержимого в Интернете.

## <a name="bing-image-search-features"></a>Возможности API Bing для поиска изображений

| Компонент                                                                                                                                                                                 | Описание                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Предложение условий поиска в режиме реального времени](./concepts/bing-image-search-sending-queries.md) | Улучшите работу приложения, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), отображающий предлагаемые условия поиска по мере их ввода. |
| [Фильтрация и ограничение результатов поиска изображений](./concepts/bing-image-search-get-images.md)                       | Отфильтруйте возвращаемые Bing изображения путем изменения параметров запроса.                                                                                                       |
| [Обрезка, изменения размера и отображение эскизов](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Изменяйте и отображайте предварительные версии эскизов для изображений, возвращаемых API Bing для поиска изображений.                                                                                      |
| [Сводка и расширение пользовательских поисковых запросов](./concepts/bing-image-search-sending-queries.md)               | Расширьте возможности поиска путем включения и отображения в запросах предлагаемых Bing условий поиска.                                                                    |
| [Получение изображений, набирающих популярность](trending-images.md)                                                                     | Настройте поиск популярных изображений со всего мира.                                                                                                          |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска изображений является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализ JSON. Вы можете использовать службу с помощью [REST API](./quickstarts/csharp.md) или [пакета SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Создайте [учетную запись API Cognitive Services](../cognitive-services-apis-create-account.md) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/free/cognitive-services/).
2. Отправьте запрос к API с допустимым [поисковым запросом](./concepts/bing-image-search-sending-queries.md).
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.

## <a name="next-steps"></a>Дальнейшие действия

Сначала попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) API Bing для поиска изображений.
В этой демонстрации показано, как можно быстро настроить поисковый запрос и найти изображение в Интернете.

Чтобы быстро начать работу со своим первым запросом API, научитесь:

* [отправлять поисковые запросы в Bing](./quickstarts/csharp.md) с помощью REST API или
* [запрашивать и фильтровать](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) возвращаемые Bing изображения с помощью пакета SDK.

## <a name="see-also"></a>См. также раздел

* [Сведения о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) на API-интерфейсы Поиска Bing. 

* В справочнике по [API Bing для поиска изображений версии 7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) содержатся сведения о конечных точках API, заголовках, ответах API и параметрах запросов.

* В статье [Требования к использованию и отображению API поиска Bing](../bing-web-search/use-display-requirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.

* В статье [Получение изображений из Интернета с помощью API Bing для поиска изображений](./concepts/bing-image-search-get-images.md) показано, как выполнять поиск и получать изображения из Интернета.

* В статье [Отправка запросов в API Bing для поиска изображений](./concepts/bing-image-search-sending-queries.md) приведены сведения о том, как создавать, настраивать и сводить поисковые запросы.

* Сведения о других доступных API "Поиск Bing" см. на [главной странице](../bing-web-search/overview.md).
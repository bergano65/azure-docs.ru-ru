---
title: Основные сведения об API Bing для поиска сущностей
titleSuffix: Azure Cognitive Services
description: С помощью API Bing для поиска сущностей вы можете извлекать и искать сущности и места на основе поисковых запросов.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 8f43401296a154ee40e7c214ad63da878129244a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424010"
---
# <a name="what-is-bing-entity-search-api"></a>Основные сведения об API Bing для поиска сущностей

API Bing для поиска сущностей отправляет запрос на поиск в Bing и получает результаты, которые включают сущности и места. Результаты размещения включают рестораны, гостиницы или другие местные организации. Bing возвращает расположения, если в запросе указывается имя местной организации, или запрашивает ее тип (например, рестораны рядом со мной). Bing возвращает сущности, если в запросе указаны хорошо известные люди, места (туристическая достопримечательность, штаты, страны, регионы и т. д.) или вещи.

|Функция  |ОПИСАНИЕ  |
|---------|---------|
|[Поисковые предложения, предоставляемые в режиме реального времени](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Предоставляйте поисковые предложения в виде раскрывающегося списка по мере ввода данных пользователями.       | 
| [Уточнение сущностей](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Получение нескольких сущностей для запросов с несколькими возможными значениями. |
| [Поиск мест](concepts/search-for-entities.md#find-places) | Поиск и возврат сведений о местных учреждениях и сущностях.  |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска сущностей является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализа JSON. Вы можете использовать службу с помощью REST API или пакета SDK.

1. Создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Отправьте запрос к API с допустимым поисковым запросом.
3. Обработайте ответ API путем анализа возвращенного сообщения JSON.

## <a name="next-steps"></a>Дополнительная информация

* Попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) API Bing для поиска сущностей. 
* Сведения о том, как быстро создать первый запрос, см. в [этом кратком руководстве](quickstarts/csharp.md).
* См. раздел справочника по [API Bing для поиска сущностей версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* В статье [Требования к использованию и отображению API поиска Bing](./use-display-requirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.

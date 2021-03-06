---
title: Знакомство с API Пользовательского поиска Bing
titleSuffix: Azure Cognitive Services
description: Служба "API пользовательского поиска Bing" позволяет создавать специально адаптированные интерфейсы поиска по темам, которые действительно важны для вас.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 4b0b0d91af15912e1c64761351ba33acfd3e2725
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405156"
---
# <a name="what-is-the-bing-custom-search-api"></a>Знакомство с API Пользовательского поиска Bing

Служба "API пользовательского поиска Bing" позволяет создавать специально адаптированные интерфейсы поиска без рекламы по темам, которые действительно важны для вас. Вы можете указать домены и веб-страницы для поиска Bing, а также закрепить определенное содержимое, повысить или понизить его уровень, чтобы создать пользовательское представление веб-сайта и помочь пользователю быстро найти релевантные результаты поиска. 

## <a name="features"></a>Функции

|Функция  |ОПИСАНИЕ  |
|---------|---------|
|[Пользовательские поисковые предложения, предоставляемые в режиме реального времени](define-custom-suggestions.md)     | Предоставляйте поисковые предложения в виде раскрывающегося списка по мере ввода данных пользователями.       | 
|[Возможности пользовательского поиска изображений](get-images-from-instance.md)     | Разрешите пользователям искать изображения с доменов и веб-сайтов, указанных в экземпляре пользовательского поиска.        |        
|[Возможности пользовательского поиска видео](get-videos-from-instance.md)     | Разрешите пользователям искать видео с доменов и веб-сайтов, указанных в экземпляре пользовательского поиска.        |    
|[Совместное использование экземпляра пользовательского поиска](share-your-custom-search.md)     | Совместно редактируйте и тестируйте свой экземпляр поискового запроса, предоставив к нему доступ участникам вашей команды.        | 
|[Настройка пользовательского интерфейса для приложений и веб-сайтов](hosted-ui.md)     | Совместно редактируйте и тестируйте свой экземпляр поискового запроса, предоставив к нему доступ участникам вашей команды.        | 
## <a name="workflow"></a>Рабочий процесс

Вы можете создать экземпляр пользовательского поиска с помощью [портала Пользовательского поиска Bing](https://customsearch.ai). Этот портал позволяет создать экземпляр пользовательского поиска с указанием доменов, веб-сайтов и веб-страниц, по которым будет выполняться поиск Bing, а также сайтов, по которым поиск выполнять не требуется. С помощью портала также можно предварительно просмотреть результаты поиска, изменить рейтинг поиска, предоставляемый API, и при необходимости настроить доступный для поиска пользовательский интерфейс, который будет отображаться на веб-сайтах и в приложениях.

После создания экземпляра поиска можно интегрировать его (и при необходимости пользовательский интерфейс) в веб-сайт или приложение путем вызова API пользовательского поиска Bing.

![Изображение, показывающее подключение к Пользовательскому поиску Bing с помощью API](media/BCS-Overview.png "Принцип работы Пользовательского поиска Bing.")


## <a name="next-steps"></a>Дополнительная информация

Для быстрого начала работы см. статью [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы "Пользовательский поиск Bing").

Дополнительные сведения о настройке экземпляра поиска см. в статье [Определение экземпляра пользовательского поиска](define-your-custom-view.md).

Чтобы узнать больше об использовании поисковых результатов в службах и приложениях, прочтите статью [Требования к использованию и отображению API-интерфейсов поиска Bing](./use-and-display-requirements.md).

Ознакомьтесь со справочными материалами по каждой конечной точке пользовательского поиска. Руководство содержит список конечных точек, заголовков и параметров запроса, которые будут использоваться для запроса результатов поиска. Оно также содержит определения объектов ответа.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API пользовательского поиска](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API пользовательского изображения](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API для поиска пользовательских видео](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API пользовательского автозаполнения](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)


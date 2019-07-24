---
title: Что такое API Поиска местных компаний Bing
titleSuffix: Azure Cognitive Services
description: API Bing Local Business Search — это служба RESTful, которая позволяет приложениям находить сведения о местных заведениях и компаниях на основе поисковых запросов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2078e4842f6b8af11fcf56760579ce1ec77dd23f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423364"
---
# <a name="what-is-bing-local-business-search"></a>Что такое Bing Local Business Search?
API Bing Local Business Search — это служба RESTful, которая позволяет приложениям находить сведения о местных компаниях на основе поисковых запросов. Например, `q=<business-name> in Redmond, Washington` или `q=Italian restaurants near me`. 

## <a name="features"></a>Компоненты
| Компонент | Описание |  
| -- | -- | 
| [Поиск местных компаний и мест](quickstarts/local-quickstart.md) | API Bing Local Business Search получает локализованные результаты из запроса. Результаты включают в себя URL-адрес веб-сайта компании, и в них отображается текст, номер телефона и географическое положение, включая координаты GPS, город и улицу |  
| [Фильтрация результатов местного поиска с помощью географических границ](specify-geographic-search.md) | В качестве параметров поиска добавьте координаты, чтобы ограничить результаты определенной географической областью, заданной круговой областью или ограничивающим прямоугольником. | 
| [Фильтрация результатов поиска местных компаний по категориям](local-categories.md) | Проводите поиск местных компаний по категориям. Этот параметр использует обратное расположение IP-адресов или координат GPS вызывающей стороны, чтобы возвращать локализованные результаты в различных категориях компаний.|

## <a name="workflow"></a>Рабочий процесс
Вызывайте API Bing Local Business Search с помощью любого языка программирования, который может отправлять HTTP-запросы и анализировать ответы JSON. Эта служба доступна через REST API.
 
1. Создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, вы можете [создать бесплатную учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Закодируйте условия поиска в URL-адресе параметра запроса `q=""`. Например, `q=nearby+restaurant` или `q=nearby%20restaurant`. При необходимости также задайте разбиение на страницы. 
3. Отправьте [запрос к API Bing Local Business Search](quickstarts/local-quickstart.md). 
4. Проанализируйте ответ JSON. 

> [!NOTE]
> Сейчас Local Business Search поддерживает только рынок `en-US`. 
> [!NOTE]
> Сейчас Local Business Search не поддерживает автозаполнение. 

## <a name="next-steps"></a>Следующие шаги
- [Sending and using Bing Local Business Search API queries and responses](local-search-query-response.md) (Отправление и использование запросов и ответов API Bing Local Business Search)
- [Quickstart: Send a query to the Bing Local Business Search API in C#](quickstarts/local-quickstart.md) (Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью C#)
- [Bing Local Business Search API v7 reference](local-search-reference.md) (Справочник по API Bing Local Business Search версии 7)
- [Требования к использованию и отображению](use-display-requirements.md)

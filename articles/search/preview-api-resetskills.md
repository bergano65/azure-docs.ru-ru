---
title: Сброс навыков (API-Version = 2019 – 050 -06-Preview)
titleSuffix: Azure Cognitive Search
description: Предварительный просмотр REST API используется для добавочного обогащения, если требуется полная или частичная повторная обработка набора навыков.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832159"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Сброс навыков (API-Version = 2019 – 05 -06-Preview)

> [!IMPORTANT] 
> Добавочное углубление в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время отсутствует поддержка портала или пакета SDK для .NET.

Запрос на **Сброс навыков** указывает, какие навыки должны обрабатываться при следующем выполнении индексатора. Для индексаторов, для которых включено кэширование, можно явно запрашивать обработку для обновлений, которые не может обнаружить индексатор. Например, если вы вносите внешние изменения, например изменения в пользовательский навык, вы можете использовать этот API для повторного выполнения навыка. Выходные данные, такие как хранилище знаний или индекс поиска, обновляются с помощью повторно используемых данных из кэша и нового содержимого в соответствии с обновленным навыком.

Можно сбросить существующий набор [навыков](https://docs.microsoft.com/rest/api/searchservice/create-skillset) с помощью HTTP-размещения, указав имя набора навыков для обновления в URI запроса. Для запроса необходимо использовать **API-Version = 2019 – 05 -06-Preview** .

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Кроме того, можно использовать POST и поместить имя индексатора в текст запроса:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Заголовки запросов  

 Таблица ниже содержит обязательные и необязательные заголовки запроса.  

|Заголовок запроса|Description|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Для этого заголовка необходимо задать значение `application/json`|  
|*api-key:*|Обязательный элемент. Заголовок `api-key` используется для проверки подлинности запроса в службе поиска. Это уникальное строковое значение, присваиваемое службе. Запрос на **Сброс набора навыков** должен включать заголовку `api-key`, заданному в качестве ключа администратора (в отличие от ключа запроса).|  

Для формирования URL-адреса запроса также требуется имя службы. Вы можете получить имя службы и `api-key` на странице обзора службы в портал Azure. См. раздел [Создание службы когнитивный Поиск Azure](https://docs.microsoft.com/azure/search/search-create-service-portal) для навигации по страницам.  

## <a name="request-body-syntax"></a>Синтаксис текста запроса  

Текст запроса представляет собой массив названий навыков.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Ответ  

Код состояния: в качестве успешного ответа возвращается код «204 — Нет содержимого». 

## <a name="see-also"></a>См. также

+ [Поиск в API-интерфейсах RESTFUL](https://docs.microsoft.com/rest/api/searchservice)
+ [Коды состояния HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Обзор обогащения искусственного интеллекта](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Настройка кэширования и добавочного обогащения](search-howto-incremental-index.md)
+ [Добавочное углубление](cognitive-search-incremental-indexing-conceptual.md)
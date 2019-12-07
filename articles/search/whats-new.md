---
title: Новые объявления о функциях
titleSuffix: Azure Cognitive Search
description: Объявления о новых и улучшенных функциях, включая переименование службы поиска Azure в Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1df328f151a4085ec0aadd1b880048f81483a51
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901325"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Новые возможности Azure Когнитивный поиск

Узнайте о новых возможностях службы. Добавьте на эту страницу закладку, чтобы иметь актуальную версию службы.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Новое имя службы для поиска Azure

Теперь служба поиска Azure переименована в **azure когнитивный Поиск** , чтобы отразить расширенное использование функций для работы с обучением и обработки искусственного интеллекта в основных операциях. В то время как функции «автообучение» добавляют новые возможности, использование AI является строго необязательным. Вы можете по-прежнему использовать Azure Когнитивный поиск без искусственного интеллекта для создания полнофункциональных, полнотекстовых поисковых решений по закрытому, разнородных текстовому содержимому в индексе, который вы создаете и управляете в облаке. 

Версии API, пакеты NuGet, пространства имен и конечные точки не изменяются. Изменение имени службы не влияет на имеющиеся решения поиска.

## <a name="feature-announcements"></a>Объявления о функциях

### <a name="december-2019"></a>Декабрь 2019 г.

+ [Создание приложения (Предварительная версия)](search-create-app-portal.md) — это новый мастер на портале, который создает загружаемый HTML-файл. Файл включает внедренный скрипт, который отображает рабочее веб-приложение типа localhost, привязанное к индексу в службе поиска. Страницы настраиваются в мастере и могут содержать панель поиска, область результатов, навигацию по боковой панели и поддержку запросов typeahead. Можно изменить HTML в автономном режиме, чтобы расширить или настроить рабочий процесс или внешний вид.

### <a name="november-2019---ignite-conference"></a>Ноябрь 2019-Ignite Конференция

+ [Добавочное индексирование (Предварительная версия)](cognitive-search-incremental-indexing-conceptual.md) позволяет выбрать, какие шаги следует обработать при внесении изменений в конвейер обогащения. Добавочное индексирование полезно при наличии ранее проанализированного содержимого. Выходные данные дорогостоящего анализа хранятся, а затем используются в качестве базиса для дополнительного индексирования или дополнения.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Извлечение документов (Предварительная версия)](cognitive-search-skill-document-extraction.md) — это восприятный навык, используемый во время индексирования, который позволяет извлекать содержимое файла из набора навыков. Ранее перед выполнением набора навыков произошло взлом документов. С добавлением этого навыка можно также выполнить эту операцию в рамках выполнения набора навыков.

+ [Преобразование текста (Предварительная версия)](cognitive-search-skill-text-translation.md) — это восприятный навык, используемый во время индексирования, который оценивает текст и для каждой записи возвращает текст, преобразованный на указанный целевой язык.

+ [Шаблоны Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) могут быстро приступить к визуализациям и анализу расширенного содержимого в хранилище знаний в Power BI Desktop. Этот шаблон предназначен для проектов таблиц Azure, созданных с помощью [мастера импорта данных](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage 2-го поколения (Предварительная версия)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB API Gremlin (Предварительная](search-howto-index-cosmosdb.md)версия) и [Cosmos DB API Cassandra (Предварительная версия)](search-howto-index-cosmosdb.md) теперь поддерживаются в индексаторах. Вы можете зарегистрироваться, используя [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). После того как вы примете предварительную версию программы, вы получите сообщение электронной почты с подтверждением.

### <a name="july-2019"></a>Июль 2019 г.

+ Общедоступно в [облаке Azure для государственных организаций](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Обновления службы

[Объявления об обновлении службы](https://azure.microsoft.com/updates/?product=search&status=all) для когнитивный Поиск Azure можно найти на веб-сайте Azure.
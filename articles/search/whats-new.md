---
title: Новые объявления о функциях
titleSuffix: Azure Cognitive Search
description: Объявления о новых и улучшенных функциях, включая переименование службы поиска Azure в Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834388"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Новые возможности Azure Когнитивный поиск

Узнайте о новых возможностях службы. Добавьте на эту страницу закладку, чтобы иметь актуальную версию службы.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Новое имя службы для поиска Azure

Теперь служба поиска Azure переименована в **azure когнитивный Поиск** , чтобы отразить расширенное использование функций для работы с обучением и обработки искусственного интеллекта в основных операциях. В то время как функции «автообучение» добавляют новые возможности, использование AI является строго необязательным. Вы можете по-прежнему использовать Azure Когнитивный поиск без искусственного интеллекта для создания полнофункциональных, полнотекстовых поисковых решений по закрытому, разнородных текстовому содержимому в индексе, который вы создаете и управляете в облаке. 

Версии API, пакеты NuGet, пространства имен и конечные точки не изменяются. Изменение имени службы не влияет на имеющиеся решения поиска.

## <a name="feature-announcements"></a>Объявления о функциях

### <a name="january-2020"></a>Январь 2020 г.

+ [Ключи шифрования, управляемые клиентом](search-security-manage-encryption-keys.md) , теперь общедоступны. Если вы используете функцию RESTFUL, доступ к этой функции можно получить с помощью `api-version=2019-05-06`. Для управляемого кода правильный пакет по-прежнему является [пакетом SDK для .NET версии 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , хотя эта функция находится в предварительной версии. 

+ *Доступ к ограниченному IP-адресу и закрытой конечной точке (Предварительная версия)* в конечной точке службы поиска теперь доступен в **API-Version = 2019 – 10 01-Preview**. Вы можете настроить безопасную конечную точку с помощью новых свойств **ипруле** и **нетворкрулесет** в REST API управления [созданием или обновлением](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) . Дополнительные сведения о версиях API и региональных доступности см. [в разделе использование REST API управления](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Декабрь 2019 г.

+ [Создание приложения (Предварительная версия)](search-create-app-portal.md) — это новый мастер на портале, который создает загружаемый HTML-файл. Файл включает внедренный скрипт, который отображает рабочее веб-приложение типа localhost, привязанное к индексу в службе поиска. Страницы настраиваются в мастере и могут содержать панель поиска, область результатов, навигацию по боковой панели и поддержку запросов typeahead. Можно изменить HTML в автономном режиме, чтобы расширить или настроить рабочий процесс или внешний вид.

### <a name="november-2019---ignite-conference"></a>Ноябрь 2019-Ignite Конференция

+ [Добавочное дополнение (Предварительная версия)](cognitive-search-incremental-indexing-conceptual.md) добавляет кэширование и статефуллнесс в конвейер обогащения, чтобы можно было работать с определенными шагами или этапами без потери уже обработанного содержимого. Ранее при любом изменении конвейера обогащения требовалось полное перестроение. При добавочном дополнении сохраняются выходные данные дорогостоящего анализа, особенно анализ изображений.

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
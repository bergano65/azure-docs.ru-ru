---
title: Новые объявления о функциях
titleSuffix: Azure Cognitive Search
description: Объявления о новых и улучшенных функциях, включая переименование службы поиска Azure в Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 24114defe5e78bb7adbd006adc4ddbf8250b3188
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844978"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Новые возможности Azure Когнитивный поиск

Узнайте о новых возможностях службы. Добавьте на эту страницу закладку, чтобы иметь актуальную версию службы.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Новое имя службы

Теперь служба поиска Azure переименована в **Azure когнитивный Поиск** для отражения расширенных (но необязательных) возможностей использования функций и обработки искусственного интеллекта в основных операциях. Версии API, пакеты NuGet, пространства имен и конечные точки не изменяются. Изменение имени службы не влияет на новые и существующие решения поиска.

## <a name="feature-announcements"></a>Объявления о функциях

### <a name="february-2020"></a>Февраль 2020 г.

+ [Обнаружение](cognitive-search-skill-pii-detection.md) персональных данных — это непонятный навык, используемый во время индексирования, который извлекает персональные данные из входного текста и дает возможность маскировать его из этого текста различными способами.

### <a name="january-2020"></a>Январь 2020 г.

+ [Ключи шифрования, управляемые клиентом](search-security-manage-encryption-keys.md) , теперь общедоступны. Если вы используете функцию RESTFUL, доступ к этой функции можно получить с помощью `api-version=2019-05-06`. Для управляемого кода правильный пакет по-прежнему является [пакетом SDK для .NET версии 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , хотя эта функция находится в предварительной версии. 

+ Закрытый доступ к службе поиска доступен в двух механизмах:

  + Для создания службы можно ограничить доступ к определенным IP-адресам с помощью `api-version=2019-10-01-Preview` REST API управления. API предварительной версии содержит новые свойства **ипруле** и **Нетворкрулесет** в [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate). Эта предварительная версия функции доступна в выбранных регионах. Дополнительные сведения см. [в разделе использование REST API управления](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + В настоящее время в предварительной версии с ограниченным доступом можно подготавливать службу поиска Azure, которая поддерживает частные конечные точки Azure для подключений клиентов в той же виртуальной сети. Дополнительные сведения см. в статье [Создание частной конечной точки для безопасного подключения](service-create-private-endpoint.md).

### <a name="december-2019"></a>Декабрь 2019 г.

+ [Создание приложения (Предварительная версия)](search-create-app-portal.md) — это новый мастер на портале, который создает загружаемый HTML-файл. Файл включает внедренный скрипт, который отображает рабочее веб-приложение типа localhost, привязанное к индексу в службе поиска. Страницы настраиваются в мастере и могут содержать панель поиска, область результатов, навигацию по боковой панели и поддержку запросов typeahead. Можно изменить HTML в автономном режиме, чтобы расширить или настроить рабочий процесс или внешний вид.

+ [Создание частной конечной точки для безопасных подключений (Предварительная версия)](service-create-private-endpoint.md) объясняет, как настроить частную ссылку для безопасных подключений к службе поиска. Эта предварительная версия функции доступна по запросу и использует [частную связь Azure](../private-link/private-link-overview.md) и [виртуальную сеть Azure](../virtual-network/virtual-networks-overview.md) в качестве части решения.

### <a name="november-2019---ignite-conference"></a>Ноябрь 2019-Ignite Конференция

+ [Добавочное дополнение (Предварительная версия)](cognitive-search-incremental-indexing-conceptual.md) добавляет кэширование и статефуллнесс в конвейер обогащения, чтобы можно было работать с определенными шагами или этапами без потери уже обработанного содержимого. Ранее при любом изменении конвейера обогащения требовалось полное перестроение. При добавочном дополнении сохраняются выходные данные дорогостоящего анализа, особенно анализ изображений.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Извлечение документов (Предварительная версия)](cognitive-search-skill-document-extraction.md) — это восприятный навык, используемый во время индексирования, который позволяет извлекать содержимое файла из набора навыков. Ранее перед выполнением набора навыков произошло взлом документов. С добавлением этого навыка можно также выполнить эту операцию в рамках выполнения набора навыков.

+ [Преобразование текста](cognitive-search-skill-text-translation.md) — это восприятный навык, используемый во время индексирования, который оценивает текст и для каждой записи возвращает текст, преобразованный на указанный целевой язык.

+ [Шаблоны Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) могут быстро приступить к визуализациям и анализу расширенного содержимого в хранилище знаний в Power BI Desktop. Этот шаблон предназначен для проектов таблиц Azure, созданных с помощью [мастера импорта данных](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage 2-го поколения (Предварительная версия)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB API Gremlin (Предварительная](search-howto-index-cosmosdb.md)версия) и [Cosmos DB API Cassandra (Предварительная версия)](search-howto-index-cosmosdb.md) теперь поддерживаются в индексаторах. Вы можете зарегистрироваться, используя [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). После того как вы примете предварительную версию программы, вы получите сообщение электронной почты с подтверждением.

### <a name="july-2019"></a>Июль 2019 г.

+ Общедоступно в [облаке Azure для государственных организаций](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Обновления службы

[Объявления об обновлении службы](https://azure.microsoft.com/updates/?product=search&status=all) для когнитивный Поиск Azure можно найти на веб-сайте Azure.
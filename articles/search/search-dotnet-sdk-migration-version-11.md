---
title: Обновление до пакета SDK для .NET версии 11
titleSuffix: Azure Cognitive Search
description: Перенос кода в пакет SDK Azure Когнитивный поиск .NET версии 11 с предыдущих версий. Сведения о новых возможностях и необходимых изменениях кода.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 390376216700b760e96c2348b1ad61bb4561aad2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211517"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Обновление до Azure Когнитивный поиск .NET SDK версии 11

Если вы используете версию 10,0 или более раннюю версию [пакета SDK для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search), эта статья поможет вам выполнить обновление до версии 11.

Версия 11 — это полностью переработанная клиентская библиотека, выпущенная группой разработки пакета SDK для Azure (предыдущие версии были созданы командой разработчиков Когнитивный поиск Azure). Библиотека была переработана для обеспечения большей согласованности с другими клиентскими библиотеками Azure, учитывая зависимость от [Azure. Core](https://docs.microsoft.com/dotnet/api/azure.core) и [System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)и реализуя привычные подходы к распространенным задачам.

Ниже приведены некоторые ключевые отличия, которые можно заметить в новой версии.

+ Один пакет и библиотека, а не несколько
+ Новое имя пакета: `Azure.Search.Documents` вместо `Microsoft.Azure.Search` .
+ Три клиента вместо двух: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Различия в именовании в диапазоне API и небольших структурных отличий, упрощающих некоторые задачи

## <a name="package-and-library-consolidation"></a>Консолидация пакетов и библиотек

Версия 11 объединяет несколько пакетов и библиотек в одну. После миграции у вас будет меньше библиотек для управления.

+ [Azure.Search.Docпакет ументс](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Справочник по API для клиентской библиотеки](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Различия клиента

Если это применимо, в следующей таблице сопоставлены клиентские библиотеки между двумя версиями.

| Область действия | Microsoft. Azure. Search &nbsp; (V10) | Azure.Search.Docументс &nbsp; (версии 11) |
|---------------------|------------------------------|------------------------------|
| Клиент, используемый для запросов и заполнения индекса. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [сеарчклиент](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Клиент, используемый для индексов, анализаторов, сопоставлений синонимов | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Клиент, используемый для индексаторов, источников данных, навыков | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [Сеарчиндексерклиент (**новое**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` существует в обеих версиях, но поддерживает различные вещи. В версии 10 `SearchIndexClient` Создайте индексы и другие объекты. В версии 11 `SearchIndexClient` работает с существующими индексами. Чтобы избежать путаницы при обновлении кода, учитывать порядок обновления клиентских ссылок. После выполнения последовательности [действий по обновлению](#UpgradeSteps) необходимо устранить любые проблемы замены строк.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Различия в именовании и других API

Помимо отличий клиентов (отмеченных ранее, и, таким, здесь опущено), несколько других API были переименованы, а в некоторых случаях переработано. Ниже приведены отличия имен классов. Этот список не является исчерпывающим, но в нем изменения API группируются по задачам, что может быть полезным для редакций конкретных блоков кода. Детализированный список обновлений API см. в [журнале изменений](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` на GitHub.

### <a name="authentication-and-encryption"></a>Проверка подлинности и шифрование

| Версия 10 | Эквивалент версии 11 |
|------------|-----------------------|
| [сеарчкредентиалс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [азурекэйкредентиал](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (существовало в [предварительной версии пакета SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) как общедоступная функция) | [сеарчресаурцеенкриптионкэй](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Индексы, анализаторы, сопоставления синонимов

| Версия 10 | Эквивалент версии 11 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [сеарчиндекс](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Поле](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [сеарчфиелд](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [сеарчфиелддататипе](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [итемеррор](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [сеарчиндексереррор](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [Лексикаланализер](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (также `AnalyzerName` `LexicalAnalyzerName` ) |
| [анализерекуест](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [анализетекстоптионс](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [луценестандарданализер](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [стандардтокенизер](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [Луценестандардтокенизер](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (также `StandardTokenizerV2` `LuceneStandardTokenizerV2` ) |
| [токенинфо](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [анализедтокенинфо](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Разметчика](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [Лексикалтокенизер](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (также `TokenizerName` `LexicalTokenizerName` ) |
| [SynonymMap. Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Нет. Удалите ссылки на `Format` . |

Определения полей упрощаются: [сеарчаблефиелд](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [симплефиелд](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), [Комплексфиелд](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) — это новые интерфейсы API для создания определений полей.

### <a name="indexers-datasources-skillsets"></a>Индексаторы, источники данных, навыков

| Версия 10 | Эквивалент версии 11 |
|------------|-----------------------|
| [Индексатор](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [сеарчиндексер](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [сеарчиндексердатасаурцеконнектион](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Навыков](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [сеарчиндексерскилл](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Набор навыков](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [сеарчиндексерскиллсет](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [сеарчиндексердатасаурцетипе](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Импорт данных

| Версия 10 | Эквивалент версии 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [индексдокументсактион](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [индексдокументсбатч](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Определения и результаты запросов

| Версия 10 | Эквивалент версии 11 |
|------------|-----------------------|
| [документсеарчресулт](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) или [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1)в зависимости от того, является ли результат отдельным документом или несколькими. |
| [документсугжестресулт](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [сугжестресултс](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [сеарчоптионс](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Что находится в версии 11

Каждая версия клиентской библиотеки Azure Когнитивный поиск предназначена для соответствующей версии REST API. REST API считается основой службы, а отдельные пакеты SDK обтекает версию REST API. Разработчикам .NET может быть полезно ознакомиться с [REST API документацией](https://docs.microsoft.com/rest/api/searchservice/) , если вы хотите получить дополнительные сведения о конкретных объектах или операциях.

Версия 11 предназначена для [службы поиска 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Поскольку версия 11 также является новой клиентской библиотекой, созданной с нуля, большая часть усилий по разработке посвящена эквивалентности версии 10, а некоторые REST API функции все еще находятся в состоянии ожидания.

Версия 11 полностью поддерживает следующие объекты и операции:

+ Создание и управление индексами
+ Создание и управление картой синонимов
+ Все типы запросов и синтаксис (за исключением геопространственных фильтров)
+ Объекты и операции индексатора для индексирования источников данных Azure, включая источники данных и навыков

### <a name="pending-features"></a>Ожидающие функции

Следующие функции версии 10 пока недоступны в версии 11. Если вы используете эти функции, отключитесь от миграции, пока они не будут поддерживаться.

+ Геопространственные типы
+ [Фиелдбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (хотя [это решение](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)можно использовать).
+ [Хранилище знаний](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению

Следующие шаги помогут приступить к переносу кода с помощью прохода по первому набору необходимых задач, особенно в отношении клиентских ссылок.

1. Установите [Azure.Search.Docпакет ументс](https://www.nuget.org/packages/Azure.Search.Documents/) , щелкнув правой кнопкой мыши ссылку на проект и выбрав пункт "Управление пакетами NuGet...". в Visual Studio.

1. Замените директивы using для Microsoft. Azure. Search следующим:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Замените [сеарчкредентиалс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) на [азурекэйкредентиал](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Обновление клиентских ссылок для объектов, связанных с индексатором. Если используются индексаторы, источники данных или навыков, измените ссылки клиента на [сеарчиндексерклиент](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Этот клиент впервые помещается в версии 11 и не имеет предшествующей задачи.

1. Обновление клиентских ссылок для запросов и импорта данных. Экземпляры [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) должны быть изменены на [сеарчклиент](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Чтобы избежать путаницы с именами, убедитесь, что все экземпляры перехватываются перед переходом к следующему шагу.

1. Обновление клиентских ссылок для объектов index, индексатора, преобразования синонимов и анализатора. Экземпляры [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) должны быть изменены на [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Как можно скорее, обновите классы, методы и свойства, чтобы использовать API новой библиотеки. Раздел [различия имен](#naming-differences) можно запустить, но вы также можете ознакомиться с [журналом изменений](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Если у вас возникли проблемы с поиском эквивалентных API-интерфейсов, мы рекомендуем регистрировать проблему, [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) чтобы мы могли улучшить документацию или исследовать проблему.

1. Выполните повторную сборку решения. После исправления ошибок или предупреждений сборки можно внести дополнительные изменения в приложение, чтобы воспользоваться преимуществами [новых функциональных возможностей](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Критические изменения в версии 11

Учитывая изменения в библиотеках и API, обновление до версии 11 не является тривиальной задачей и является критическим изменением в том смысле, что ваш код больше не будет обратно совместим с версией 10 и более ранними версиями. Подробное рассмотрение различий см. в [журнале изменений](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) для `Azure.Search.Documents` .

В плане версий службы переход с 10 на 11 вносит следующие изменения в поведение: 

+ [Алгоритм ранжирования BM25](index-ranking-similarity.md) заменяет предыдущий алгоритм ранжирования на новую технологию. Новые службы будут использовать этот алгоритм автоматически. Для существующих служб необходимо задать параметры для использования нового алгоритма.

+ [Упорядоченные результаты](search-query-odata-orderby.md) для значений NULL были изменены в этой версии, и значения NULL появлялись первыми, если сортировка имеет значение `asc` и последний `desc` . Если вы написали код, который обрабатывает, как сортируются значения NULL, следует проверить и, возможно, удалить этот код, если он больше не нужен.

## <a name="next-steps"></a>Дальнейшие шаги

+ [Azure.Search.Docпакет ументс](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Примеры на GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [ Справочник по APIAzure.Search.Docумент](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)
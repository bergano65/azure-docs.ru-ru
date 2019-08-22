---
title: C# Пример синонимов. Поиск Azure
description: В этом C# примере показано, как добавить функцию синонимов в индекс в поиске Azure. Карта синонимов — это список эквивалентных терминов. Поля с поддержкой синонимов расширяют запросы, позволяя включать в них указанный пользователем термин и все его синонимы.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ad71a6ab5090e601ef075617edf08c421abebdb0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647756"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Пример: Добавление синонимов в Поиск Azure на C#

Синонимы позволяют расширить запрос с помощью сопоставления семантически эквивалентных терминов с входными терминами. Например, вы можете сопоставить термин "машина" с документами, в которых встречается слово "автомобиль" или "транспортное средство". 

В Поиске Azure синонимы определены в *сопоставлении синонимов* с помощью *правил сопоставления*, связывающих эквивалентные термины. В этом примере рассматриваются основные шаги по добавлению и использованию синонимов с существующим индексом. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте карту синонимов с помощью класса [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) . 
> * Задайте свойство [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) для полей, которые должны поддерживать расширение запросов через синонимы.

Вы можете запросить поле с поддержкой синонимов как обычно. Для доступа к синонимам не требуется дополнительный синтаксис запроса.

Вы можете создать несколько сопоставлений синонимов, разместить их как ресурс служб, доступный для любого индекса, а затем указать ссылку на используемый на уровне поля. Во время выполнения запроса, кроме поиска индекса, Поиск Azure обязательно просмотрит сопоставление синонимов, если один из них указан в каком-либо поле, используемом в этом запросе.

> [!NOTE]
> Синонимы можно создавать программно, но не на портале. Если вы считаете, что нам следует добавить поддержку синонимов на портале Azure, оставьте свой отзыв на [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="prerequisites"></a>Предварительные требования

Ниже приведены предварительные требования, описанные в этом руководстве.

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Служба поиска Azure](search-create-service-portal.md)
* [Библиотека Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Использование службы поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Обзор

Значение синонимов показано в запросах "до" и "после". В этом примере используйте пример приложения, которое выполняет запросы и возвращает результаты по образцу индекса. Пример приложения создает небольшой индекс с именем hotels с двумя документами. Приложение выполняет поисковые запросы с помощью терминов и фраз, которые отсутствуют в индексе, что вызывает функцию синонимов, и поиск выполняется повторно. В примере кода ниже показан общий поток.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
В статье [Использование службы поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) вы ознакомитесь с действиями по созданию и заполнению примера индекса.

## <a name="before-queries"></a>Запросы "до"

С помощью `RunQueriesWithNonExistentTermsInIndex` отправляются запросы на поиск таких выражений, как "пять звезд", "Интернет" и "отели среднего класса".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Ни в одном из двух индексированных документов нет этих выражений, поэтому мы используем следующие выходные данные из первой функции `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Включение поиска синонимов

Чтобы включить поиск синонимов, нам потребуется выполнить два действия. Сначала нам необходимо определить и отправить правила синонимов, а затем настроить поля для их использования. Описание этого процесса вы можете найти в `UploadSynonyms` и `EnableSynonymsInHotelsIndex`.

1. Добавьте сопоставление синонимов в свою службу поиска. С помощью `UploadSynonyms` мы определим четыре правила в сопоставлении синонимов desc-synonymmap и добавим его в службу.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Сопоставление синонимов должно соответствовать стандартному формату `solr` с открытым кодом. Сведения об этом формате вы найдете в разделе `Apache Solr synonym format` статьи [Synonyms in Azure Search](search-synonyms.md) (Синонимы в Поиске Azure).

2. Настройте поля, поддерживающие поиск, чтобы использовать сопоставление синонимов в определении индекса. С помощью `EnableSynonymsInHotelsIndex` мы можем включить поиск синонимов для двух полей — `category` и `tags`. Для этого необходимо задать свойство `synonymMaps` для имени добавленного сопоставления синонимов.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Когда вы добавите сопоставление синонимов, необходимость в перестроении индексов отпадет. Чтобы использовать новое сопоставление синонимов, вы можете добавить это сопоставление в свою службу, а затем изменить существующие определения полей в любом индексе. Добавление новых атрибутов никак не скажется на доступности индексов. То же относится и к отключению поиска синонимов для поля. Вы можете просто задать для пустого списка свойство `synonymMaps`.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Запросы "после"

После добавления сопоставления синонимов и обновления индекса, необходимых для использования этого сопоставления, мы получаем следующие выходные данные, вызвав функцию `RunQueriesWithNonExistentTermsInIndex` второй раз:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Первый запрос находит документ из правила `five star=>luxury`. Для второго запроса поиск расширяется с помощью термина `internet,wifi`, а для третьего при поиске соответствующих документов используются оба термина запросов — `hotel, motel` и `economy,inexpensive=>budget`.

Добавление синонимов полностью изменяет возможности поиска. В этом примере исходным запросом не удалось вернуть значимые результаты, даже если документы в нашем индексе оказались релевантными. Включив поиск синонимов, мы можем расширить индекс, чтобы включить распространенные термины, не изменяя при этом базовые данные в индексе.

## <a name="sample-application-source-code"></a>Исходный код образца приложения
Полный исходный код образца приложения, используемого в этом пошаговом руководстве, см. в репозитории [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Очистка ресурсов

Самый быстрый способ очистки после примера заключается в удалении группы ресурсов, содержащей службу поиска Azure. Теперь можно удалить группу ресурсов вместе со всем ее содержимым без возможности восстановления. На портале имя группы ресурсов находится на странице "Обзор" службы "Поиск Azure".

## <a name="next-steps"></a>Следующие шаги

В этом примере демонстрируется функция синонимов C# в коде для создания и публикации правил сопоставления, а затем вызывается сопоставление синонимов для запроса. См. дополнительные сведения о [пакете SDK для .NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) и [REST API](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Как использовать синонимы в Поиске Azure](search-synonyms.md)

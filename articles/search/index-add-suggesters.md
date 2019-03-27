---
title: Добавление typeahead запросов в индекс - поиска Azure
description: Включение упреждающих запросов действий в службе поиска Azure, создав средства подбора и составления запросов, вызывающих автозаполнения или autosuggested терминов запроса.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 5a46575f6e8a0b05b65dbf49c70bddb570b514b2
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497439"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Добавление средств подбора в индекс для typeahead в службе поиска Azure

Объект **средство подбора** представляет собой конструкцию в [индекс службы поиска Azure](search-what-is-an-index.md) , поддерживающий взаимодействие «поиска как то type». Он содержит список полей, для которых вы хотите включить входных данных typeahead query. Существует два возможных typeahead: *автозаполнения* завершения термина или фразы, при вводе, *предложения* предоставляет краткий список результатов. 

На этой странице поиска Xbox автозавершения элементов занять на новую страницу результатов поиска для этого запроса предложения являются фактические результаты, которые ведут на страницу для этой конкретной игры. Можно ограничить функции автозаполнения для одного элемента в строке поиска или указать список, подобный показанному здесь. Для предложений Вы можете предоставлять любую часть документа, который лучше всего описывает результат.

![Сравнение Visual автозаполнения и предлагаемых запросов](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual сравнение автозаполнения и предлагаемых запросов")

Для реализации этого поведения в службе поиска Azure, имеется компонент индексов и запросов. 

+ В индексе добавите средство подбора. Портала, REST API или пакет SDK для .NET можно использовать для создания средств подбора. 

+ В запросе укажите предложение или sutocomplete действие. 

> [!Important]
> Автозаполнение находится в предварительной версии, доступна в предварительной версии API-интерфейсов REST и пакета SDK для .NET и не поддерживается для рабочих приложений. 

Для каждого поля включена поддержка поиска как то type. Вы можете реализовать оба механизма typeahead, в то же решение поиска, если требуется интерфейс аналогичную, указанная на снимке экрана. Оба целевых запросов *документов* коллекцию определенных индекса и ответы возвращаются после предоставления по крайней мере три символа входной строки.

## <a name="create-a-suggester"></a>Создание средства подбора

Несмотря на то, что средство подбора имеет несколько свойств, это в основном коллекцию полей, для которых вы включаете возможность typeahead. Например, для приложения для путешествий необходимо включить упреждающий поиск по пунктам назначения, городам и достопримечательностям. Таким образом все три поля, попадут в коллекции полей.

Чтобы создать средство подбора, добавьте его к схеме индекса. Может иметь одно средство подбора в индексе (в частности, один элемент в коллекции средств подбора). 

### <a name="use-the-rest-api"></a>Использование REST API

В REST API, вы можете добавить средства подбора через [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) или [обновление индекса](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```
После создания средств подбора, добавить [API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [API автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) в логике запроса для вызова функции.

### <a name="use-the-net-sdk"></a>Использование пакета SDK для .NET

В C#, определить [средство подбора класс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Средство подбора — это коллекция, но он может принять только один элемент.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Справочные данные по свойствам

Ключевые моменты, которые уведомление об использовании средств подбора — это имя (средства подбора указываются по именам в запросе), searchMode (в настоящее время только один «analyzingInfixMatching») и список полей, для которых включен typeahead. 

Для него задаются перечисленные ниже свойства.

|Свойство      |ОПИСАНИЕ      |
|--------------|-----------------|
|`name`        |Имя средства подбора. Используйте имя средства подбора, при вызове [API REST предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [автозаполнения REST API (Предварительная версия)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Стратегия, используемая для поиска фраз кандидата. В настоящее время поддерживается только режим `analyzingInfixMatching`, в рамках которого выполняется гибкий поиск совпадений в начале и середине фраз.|
|`sourceFields`|Список из одного или нескольких полей, которые служат источником содержимого для предложений. Источниками могут быть только поля типов `Edm.String` и `Collection(Edm.String)`. Можно использовать только поля, для которых не задан настраиваемый языковой анализатор.<p/>Укажите только те поля, которые предоставляются для ожидаемых и соответствующий ответ, будь то готовая строка в строке поиска или в раскрывающемся списке.<p/>Название отеля является хорошим кандидатом, так как он имеет точность. Verbose полей, таких как описания и комментарии являются слишком плотными. Аналогичным образом повторяющиеся поля, такие как категории и теги, менее эффективны. В примерах мы включаем «категория» в любом случае для демонстрации того, что может включать несколько полей. |

## <a name="when-to-create-a-suggester"></a>Когда следует создавать средство подбора

Чтобы избежать перестроения индекса, средства подбора и поля, указанные в `sourceFields` должны создаваться в то же время.

Если добавить средство подбора в существующий индекс, где существующие поля должны быть включены в `sourceFields`, существенно изменяется определение поля и перестройка является обязательным. Дополнительные сведения см. в разделе [перестроении индекса службы поиска Azure](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Как использовать средство подбора

Как было отмечено выше можно использовать средство подбора для предлагаемых запросов и автозаполнения. 

Средство подбора ссылается на запрос, а также операции. Например, на вызов GET REST укажите либо `suggest` или `autocomplete` набора documents. REST, после создания средств подбора можно использовать [API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) или [автозаполнения API (Предварительная версия)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) в логике запроса.

Для .NET, используйте [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) или [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Пример, демонстрирующий обоих запросов, см. в разделе [пример добавления автозаполнения и предложения в службе поиска Azure](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Пример кода

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) образец содержит оба C# и кода Java и демонстрирует построение средства подбора, предлагаемых запросов, автозаполнения и применяемый для фасетной навигации. 

Он использует "песочницы" службы поиска Azure и предварительно загруженным индексом, поэтому все, что необходимо сделать, — нажмите клавишу F5, чтобы запустить его. Нет подписку или зарегистрироваться в необходимости.

## <a name="next-steps"></a>Дальнейшие действия

Мы рекомендуем использовать приведенный ниже, чтобы увидеть, как запросы формулируются.

> [!div class="nextstepaction"]
> [Пример запроса автозаполнения (Предварительная версия)](search-autocomplete-tutorial.md) 

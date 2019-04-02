---
title: Создание индекса на C# — Поиск Azure
description: Узнайте, как создать индекс на C# для полнотекстового поиска с помощью пакета SDK Поиска Azure для .NET.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/22/2019
ms.openlocfilehash: a5861faaf26962d34d1c356e29dce1be40f8716b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370590"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Краткое руководство. Часть 1. Создание индекса службы "Поиск Azure" с помощью C#

Эта статья описывает создание [индекса службы "Поиск Azure"](search-what-is-an-index.md) с помощью C# и [пакета SDK для .NET](https://aka.ms/search-sdk). Это первый из трех уроков по созданию и загрузке индекса, а также выполнению к нему запроса. Создание индекса предусматривает выполнение следующих задач:

> [!div class="checklist"]
> * Создание объекта [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet), который будет подключен к службе поиска.
> * Создание объекта [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) для передачи в качестве параметра в `Indexes.Create`.
> * Вызов метода `Indexes.Create` для `SearchServiceClient`, чтобы отправить `Index` в службу.

## <a name="prerequisites"></a>Предварительные требования

[Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (любой выпуск). Пример кода и инструкции были протестированы с помощью бесплатного выпуска Community Edition.

Получите конечную точку URL-адреса и ключ API администратора вашей службы поиска. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

  1. На портале Azure на странице **Обзор** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

  2. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

  ![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="1---open-the-project"></a>Шаг 1. Открытие проекта

Скачайте пример кода [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) на сайте GitHub. 

В файле appsettings.json замените содержимое по умолчанию на приведенный ниже пример, а затем укажите имя службы и ключ API администратора для своей службы. В качестве имени службы укажите ее название. Например, если ваш URL-адрес — https://mydemo.search.windows.net, добавьте `mydemo` в JSON-файл.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

После настройки этих значений нажмите клавишу F5, чтобы создать решение для запуска консольного приложения. Последующие шаги в данном руководстве являются иллюстрацией работы этого кода. 

Подробные сведения о поведении пакета SDK см. в статье об [использовании службы "Поиск Azure" в приложении .NET](search-howto-dotnet-sdk.md). 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>Шаг 2. Создание клиента

Чтобы начать использовать пакет SDK для .NET в службе "Поиск Azure", создайте экземпляр класса `SearchServiceClient`. Этот класс имеет несколько конструкторов. Нужный вам конструктор принимает в качестве параметров имя службы поиска и объект `SearchCredentials` . `SearchCredentials` содержит ключ API.

Следующий код находится в файле Program.cs. Он создает `SearchServiceClient` с использованием значений для имени службы поиска и ключа API, которые хранятся в файле конфигурации приложения (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` имеет свойство `Indexes`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления индексов службы поиска Azure.

> [!NOTE]
> Класс `SearchServiceClient` управляет подключениями к службе поиска. Во избежание открытия слишком большого числа подключений в приложении рекомендуется по возможности совместно использовать один экземпляр `SearchServiceClient` . Его методы поддерживают такое использование, так как являются потокобезопасными.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>Шаг 3. Создание индекса
Для создания индекса достаточно отправить один вызов в метод `Indexes.Create`. В качестве параметра этот метод принимает объект `Index`, определяющий индекс службы "Поиск Azure". Создание и инициализация объекта `Index` выполняется следующим образом:

1. Задайте имя вашего индекса в качестве значения для свойства `Name` объекта `Index`.

2. Назначьте свойству `Fields` объекта `Index` массив объектов `Field`. Самый простой способ создания объектов `Field` — вызвать метод `FieldBuilder.BuildForType`, передав класс модели для параметра типа. Свойства класса модели сопоставляются с полями индекса. Это позволяет привязать документы из индекса поиска к экземплярам класса модели.

> [!NOTE]
> Если вы не планируете использовать класс модели, индекс можно определить путем непосредственного создания объектов `Field`. Имя поля вместе с типом данных можно указать в конструкторе (или анализаторе для строковых полей). Кроме того, вы можете задать другие свойства, например `IsSearchable`, `IsFilterable` и т. д.
>
>

При проектировании индекса важно помнить об удобстве работы с поиском и бизнес-потребностях. Каждому полю необходимо назначить [атрибуты](https://docs.microsoft.com/rest/api/searchservice/Create-Index), которые определяют, какие функции поиска (фильтрация, фасетизация, сортировка и т. д.) применяются к каждому полю. Для любого свойства, не заданного явным образом, класс `Field` по умолчанию отключает соответствующую функцию поиска, пока вы специально не включите ее.

В приведенном ниже примере индекс называется hotels, а поля определяются с помощью класса модели. Каждое свойство класса модели имеет атрибуты, которые определяют связанные с поиском характеристики соответствующего поля индекса. Класс модели определяется следующим образом.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Мы тщательно выбрали атрибуты для каждого свойства в зависимости от того, как мы планируем использовать их в приложении. Например, вполне вероятно, что людей, которые ищут гостиницы, будут интересовать совпадения по ключевым словам в поле `description`, поэтому мы включаем для него полнотекстовый поиск, добавив атрибут `IsSearchable` для свойства `Description`.

Обратите внимание, что только одно поле в индексе типа `string` должно быть назначено как поле *key*, для чего нужно добавить атрибут `Key` (см. `HotelId` в примере выше).

В приведенном выше определении индекса используется языковой анализатор для поля `description_fr`, так как оно предназначено для текста на французском языке. Дополнительные сведения см. в статье [Add language analyzers to an Azure Search index](index-add-language-analyzers.md) (Добавление анализатора языка в индекс Поиска Azure).

> [!NOTE]
> По умолчанию имя каждого свойства в классе модели соответствует имени поля в индексе. Если необходимо сопоставить имена всех свойств с именами полей в нижнем регистре, пометьте класс с помощью атрибута `SerializePropertyNamesAsCamelCase`. Если требуется выполнить сопоставление с другим именем, можно использовать атрибут `JsonProperty`, как в свойстве `DescriptionFr` выше. Атрибут `JsonProperty` имеет приоритет над атрибутом `SerializePropertyNamesAsCamelCase`.
> 
> 

Теперь, когда мы определили класс модели, можно легко создать определение индекса.

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>Шаг 4. Вызов метода Indexes.Create
Теперь, когда у нас есть инициализированный объект `Index`, создайте индекс, вызвав метод `Indexes.Create` для объекта `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

В случае успешного запроса метод возвращает обычные данные. При наличии проблем с запросом, например недопустимого параметра, метод вызывает исключение `CloudException`.

Если вы завершили работу с индексом и хотите удалить его, вызовите `Indexes.Delete` для `SearchServiceClient`. Например: 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенных выше примерах можно использовать `CreateAsync` и `DeleteAsync` вместо `Create` и `Delete`.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
В рамках этого краткого руководства вы создали пустой индекс службы "Поиск Azure" на основе схемы, определяющей типы данных и поведение полей. Это простой индекс, состоящий из имени и коллекции полей с атрибутом. Более реалистичный индекс будет содержать и другие элементы, такие как [профили оценки](index-add-scoring-profiles.md), [средства подбора](index-add-suggesters.md) для поддержки опережающего ввода, [синонимы](search-synonyms.md) и, возможно, [ пользовательские анализаторы](index-add-custom-analyzers.md). Когда вы изучите базовый рабочий процесс, мы рекомендуем пересмотреть эти возможности.

В следующем кратком руководстве этой серии рассматривается загрузка индекса с доступным для поиска содержимым.

> [!div class="nextstepaction"]
> [Отправка данных в службу поиска Azure с помощью пакета SDK для .NET](search-import-data-dotnet.md)
---
title: Использование интерфейсов API и пакетов SDK для Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Узнайте, как работать с API-интерфейсами цифровых двойников Azure, включая Via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73e23f24717616ef417460d2fdae6116cfdbdb3a
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810417"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Использование интерфейсов API и пакетов SDK для Azure Digital Twins

Azure Digital двойников оснащен **интерфейсами API плоскости управления** и **интерфейсами API плоскости данных** для управления экземпляром и его элементами. В этой статье приводятся общие сведения о доступных интерфейсах API и методах взаимодействия с ними. Вы можете использовать API-интерфейсы RESTFUL непосредственно со связанными с ними Swagger или с помощью пакета SDK.

## <a name="overview-control-plane-apis"></a>Обзор: API-интерфейсы плоскости управления

API-интерфейсы плоскости управления используются для управления экземпляром Azure Digital двойников в целом, поэтому они охватывают такие операции, как создание или удаление всего экземпляра. Они также будут использоваться для создания и удаления конечных точек.

Самая последняя версия API плоскости управления для общедоступной предварительной версии — _**2020-03-01-Preview**_.

Для использования API-интерфейсов плоскости управления:
* Интерфейсы API можно вызывать напрямую, ссылаясь на последнюю [папку Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). Этот репозиторий также содержит папку примеров, демонстрирующих использование.
* В настоящее время вы можете получить доступ к пакетам SDK для API управления в...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([Исходный код](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([ссылка [автоматически созданная]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([источник](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([ссылка [автоматически созданная]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Исходный](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)код)
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([источник](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go-Source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

Вы также можете использовать API-интерфейсы плоскости управления, взаимодействуя с Azure Digital двойников через [портал Azure](https://portal.azure.com) и [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Обзор: API-интерфейсы плоскости данных

API-интерфейсы плоскости данных используются для управления элементами в вашем экземпляре Digital двойников Azure. Они включают такие операции, как создание маршрутов, передача моделей, создание связей и управление двойников. Их можно разделить на следующие категории:
* **Дигиталтвинсмоделс** — Категория Дигиталтвинсмоделс содержит API-интерфейсы для управления [моделями](concepts-models.md) в экземпляре Azure Digital двойников. К действиям управления относятся передача, проверка, извлечение и удаление моделей, созданных в ДТДЛ.
* **Дигиталтвинс** — Категория Дигиталтвинс содержит API-интерфейсы, которые позволяют разработчикам создавать, изменять и удалять [Цифровые двойников](concepts-twins-graph.md) и их связи в экземпляре Digital двойников Azure.
* **Запрос** . Категория запроса позволяет разработчикам [находить наборы цифровых двойников в двойника графе](how-to-query-graph.md) по связям.
* **Евентраутес** — Категория Евентраутес содержит API-интерфейсы для [маршрутизации данных](concepts-route-events.md)через систему и в подчиненные службы.

Самая последняя версия API плоскости данных для общедоступной предварительной версии — _**2020-05-31-Preview**_. Версия API _2020-03-01-Preview_ для операций с плоскостью данных теперь устарела.

Чтобы использовать API-интерфейсы плоскости данных, выполните следующие действия.
* Интерфейсы API можно вызывать напрямую, по...
   - Ссылка на последнюю [папку Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Этот репозиторий также содержит папку примеров, демонстрирующих использование. 
   - Просмотр [справочной документации по API](https://docs.microsoft.com/rest/api/azure-digitaltwins/).
* Вы можете использовать пакет SDK для .NET (C#). В настоящее время это единственный опубликованный пакет SDK для взаимодействия с этими API. Для использования пакета SDK для .NET...
   - пакет можно просмотреть в NuGet: [Azure. дигиталтвинс. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Вы можете найти источник SDK, включая папку примеров, в GitHub: [клиентскую библиотеку Azure IOT Digital двойников для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Вы можете просмотреть [справочную документацию по пакету SDK](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview).
   - подробные сведения и примеры использования можно просмотреть, перейдя к разделу [SDK .NET (C#) (панель данных)](#net-c-sdk-data-plane) этой статьи.
* Вы можете создать пакет SDK для другого языка с помощью функции автоотдыха. Следуйте инструкциям в разделе [*инструкции по созданию настраиваемых пакетов SDK для Azure Digital двойников с помощью автоrestful*](how-to-create-custom-sdks.md).

Вы также можете использовать API-интерфейсы плоскости с датами, взаимодействуя с Azure Digital двойников через интерфейс [командной строки](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>Пакет SDK для .NET (C#) (плоскость данных)

Пакет SDK Azure Digital двойников .NET (C#) является частью пакета Azure SDK для .NET. Это открытый исходный код, основанный на API-интерфейсах плоскости данных двойников Azure Digital.

> [!NOTE]
> Подробные сведения о разработке пакетов SDK см. в общих [принципах разработки для пакетов SDK для Azure](https://azure.github.io/azure-sdk/general_introduction.html) и конкретных [рекомендациях по проектированию .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Чтобы использовать пакет SDK, включите в проект пакет NuGet **Azure. дигиталтвинс. Core** . Потребуется также пакет **Azure. Identity** .

* В Visual Studio можно добавлять пакеты с помощью диспетчера пакетов NuGet (доступ к которому осуществляется с помощью *средств > диспетчера пакетов nuget > управления пакетами NuGet для решения*). 
* С помощью программы командной строки .NET можно запустить:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Подробное пошаговое руководство по использованию API на практике см. в [*руководстве по созданию кода для клиентского приложения*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Примеры использования пакета SDK для .NET

Ниже приведены примеры кода, иллюстрирующие использование пакета SDK для .NET.

Проверка подлинности в службе:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Передать модели и список моделей:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Создание и запрос двойников:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Пошаговое руководство по коду примера приложения см. в [*руководстве по созданию кода для клиентского приложения*](tutorial-code.md) . 

Дополнительные примеры можно также найти в [репозитории GitHub для пакета SDK для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Вспомогательные методы сериализации

Как было сказано ранее, основные методы пакета SDK возвращают данные двойника в формате JSON. Однако пакет SDK также содержит вспомогательные классы для сериализации. Эти вспомогательные функции позволяют быстро создавать или десериализовать данные двойника для доступа к основным сведениям.

Доступны следующие вспомогательные классы:
* `BasicDigitalTwin`— Представляет основные данные цифрового двойникаа.
* `BasicRelationship`— Представляет основные данные связи.
* `UpdateOperationUtility`— Представляет сведения об исправлении JSON, используемые при вызове Update.
* `WriteableProperty`: Представляет метаданные свойства

##### <a name="deserialize-a-digital-twin"></a>Десериализация цифрового двойника

Вы всегда можете десериализовать данные двойника, используя библиотеку JSON по своему усмотрению, например `System.Test.Json` или `Newtonsoft.Json` . Для базового доступа к двойника вспомогательные классы делают это немного более удобным.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

`BasicDigitalTwin`Вспомогательный класс также предоставляет доступ к свойствам, определенным в двойника, через `Dictionary<string, object>` . Чтобы вывести список свойств двойника, можно использовать:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Создание цифрового двойника

С помощью `BasicDigitalTwin` класса можно подготовить данные для создания экземпляра двойника:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

Приведенный выше код эквивалентен следующему варианту "вручную":

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Десериализация связи

Данные отношений можно всегда десериализовать с помощью библиотеки JSON по своему усмотрению, например `System.Test.Json` или `Newtonsoft.Json` . Для базового доступа к связи вспомогательные классы делают это немного более удобным.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`Вспомогательный класс также предоставляет доступ к свойствам, определенным для связи, через `Dictionary<string, object>` . Чтобы вывести список свойств, можно использовать:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Создание связи

С помощью `BasicDigitalTwin` класса можно также подготовить данные для создания связей в экземпляре двойника:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Создание исправления для обновления двойника

Вызовы Update для двойников и связей используют структуру [исправления JSON](http://jsonpatch.com/) . Чтобы создать списки операций обновления JSON, можно использовать `UpdateOperationsUtility` класс, как показано ниже.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Общие примечания об использовании API/SDK

> [!NOTE]
> Обратите внимание, что на этапе предварительной версии Azure Digital двойников не поддерживает **общий доступ к ресурсам между источниками (CORS)**. В результате, если вы вызываете REST API из приложения браузера, интерфейса [управления API (APIM)](../api-management/api-management-key-concepts.md) или соединителя [Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview) , может появиться сообщение об ошибке политики.
> Чтобы устранить эту ошибку, выполните одно из следующих действий.
> * Удалить Заголовок CORS `Access-Control-Allow-Origin` из сообщения. Этот заголовок указывает, можно ли предоставить общий доступ к ответу. 
> * Кроме того, можно создать прокси-сервер CORS и сделать REST API Azure Digital двойников. 

В следующем списке приведены дополнительные сведения и общие рекомендации по использованию API и пакетов SDK.

* Чтобы использовать пакет SDK, создайте экземпляр `DigitalTwinsClient` класса. Конструктору требуются учетные данные, которые можно получить с помощью различных методов проверки подлинности в `Azure.Identity` пакете. Дополнительные сведения `Azure.Identity` см. в [документации по пространству имен](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet). 
* Вы можете найти `InteractiveBrowserCredential` полезную информацию при начале работы, но есть несколько других вариантов, включая учетные данные для [управляемого удостоверения](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet), которые, скорее всего, будут использоваться для проверки подлинности [функций Azure, НАСТРОЕНных с помощью MSI](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) в Azure Digital двойников. Дополнительные сведения о см `InteractiveBrowserCredential` . в [документации по его классу](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet).
* Все вызовы API служб предоставляются как функции-члены `DigitalTwinsClient` класса.
* Все функции службы существуют в синхронных и асинхронных версиях.
* Все функции службы создают исключение для любого возвращаемого состояния, равного 400 или выше. Убедитесь, что вызовы заносятся в `try` раздел и перехватите по крайней мере `RequestFailedExceptions` . Дополнительные сведения об этом типе исключения см. [здесь](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet).
* Большинство методов службы возвращают `Response<T>` или ( `Task<Response<T>>` для асинхронных вызовов), где `T` — это класс возвращаемого объекта для вызова службы. [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet)Класс инкапсулирует возвращаемые службы и представляет возвращаемые значения в `Value` поле.  
* Методы службы с страничными результатами возвращают `Pageable<T>` или `AsyncPageable<T>` как результаты. Дополнительные сведения о `Pageable<T>` классе см. здесь [here](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview). Дополнительные сведения `AsyncPageable<T>` см. [здесь](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview).
* Можно выполнять итерацию по страничным результатам с помощью `await foreach` цикла. Дополнительные сведения об этом процессе см. [здесь](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Базовый пакет SDK — `Azure.Core` . См. [документацию по пространству имен Azure](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) для получения справки по инфраструктуре и типам пакета SDK.

Методы службы возвращают строго типизированные объекты везде, где это возможно. Однако так как Azure Digital двойников основан на моделях, настраиваемых пользователем во время выполнения (через модели ДТДЛ, загруженные в службу), многие API службы принимают и возвращают данные двойника в формате JSON.

## <a name="monitor-api-metrics"></a>Мониторинг метрик API

Метрики API, такие как запросы, задержка и частота сбоев, можно просмотреть в [портал Azure](https://portal.azure.com/). 

На домашней странице портала найдите свой экземпляр Azure Digital двойников, чтобы получить сведения о нем. Выберите параметр **метрики** в меню экземпляра Azure Digital двойников, чтобы открыть страницу *метрики* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Снимок экрана со страницей "метрики" для Azure Digital двойников":::

Здесь можно просмотреть метрики для своего экземпляра и создать пользовательские представления.

## <a name="next-steps"></a>Дальнейшие шаги

Сведения об использовании API для настройки экземпляра и проверки подлинности Azure Digital двойников:
* [*Пошаговое руководство. Настройка экземпляра и проверки подлинности*](how-to-set-up-instance-scripted.md)

Или пошаговые инструкции по созданию клиентского приложения, такого как, которое использовалось в этом пошаговом руководстве:
* [*Руководство. Написание кода для клиентского приложения*](tutorial-code.md)

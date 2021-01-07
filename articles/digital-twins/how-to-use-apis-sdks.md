---
title: Использование интерфейсов API и пакетов SDK для Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Узнайте, как работать с API-интерфейсами цифровых двойников Azure, включая Via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9119af718131808bce0440934d482a53e39b8ef7
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964581"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Использование интерфейсов API и пакетов SDK для Azure Digital Twins

Azure Digital двойников оснащен **интерфейсами API плоскости управления** и **интерфейсами API плоскости данных** для управления экземпляром и его элементами. 
* API-интерфейсы плоскости управления — это [Azure Resource Manager API (ARM)](../azure-resource-manager/management/overview.md) и охватывающие операции управления ресурсами, такие как создание и удаление экземпляра. 
* API-интерфейсы плоскости данных — это интерфейсы API Azure Digital двойников, которые используются для операций управления данными, таких как управление моделями, двойников и граф.

В этой статье приводятся общие сведения о доступных интерфейсах API и методах взаимодействия с ними. Вы можете использовать интерфейсы API RESTFUL напрямую со связанными с ними Swagger (с помощью такого средства, как [Posting](how-to-use-postman.md)) или с помощью пакета SDK.

## <a name="overview-control-plane-apis"></a>Обзор: API-интерфейсы плоскости управления

API-интерфейсы плоскости управления — это интерфейсы API [ARM](../azure-resource-manager/management/overview.md) , используемые для управления экземпляром Azure Digital двойников в целом, поэтому они охватывают такие операции, как создание или удаление всего экземпляра. Они также будут использоваться для создания и удаления конечных точек.

Самая последняя версия API плоскости управления — _**2020-12-01**_.

Для использования API-интерфейсов плоскости управления:
* Интерфейсы API можно вызывать напрямую, ссылаясь на последнюю папку Swagger в [репозитории Swagger плоскости управления](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). В эту папку также входит папка примеров, в которых показано использование.
* В настоящее время вы можете получить доступ к пакетам SDK для API управления в...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([ссылка [автоматически созданная]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([источник](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_10_31/azure-mgmt-digitaltwins/1.0.0/jar) ([ссылка [автоматически созданная]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([источник](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/mgmt-v2020_10_31))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Исходный](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)код)
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([источник](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([источник](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

Вы также можете использовать API-интерфейсы плоскости управления, взаимодействуя с Azure Digital двойников через [портал Azure](https://portal.azure.com) и [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Обзор: API-интерфейсы плоскости данных

API-интерфейсы плоскости данных — это API-интерфейсы Azure Digital двойников, используемые для управления элементами в вашем экземпляре цифрового двойников Azure. Они включают такие операции, как создание маршрутов, передача моделей, создание связей и управление двойников. Их можно разделить на следующие категории:
* **Дигиталтвинмоделс** — Категория Дигиталтвинмоделс содержит API-интерфейсы для управления [моделями](concepts-models.md) в экземпляре Azure Digital двойников. К действиям управления относятся передача, проверка, извлечение и удаление моделей, созданных в ДТДЛ.
* **Дигиталтвинс** — Категория Дигиталтвинс содержит API-интерфейсы, которые позволяют разработчикам создавать, изменять и удалять [Цифровые двойников](concepts-twins-graph.md) и их связи в экземпляре Digital двойников Azure.
* **Запрос** . Категория запроса позволяет разработчикам [находить наборы цифровых двойников в двойника графе](how-to-query-graph.md) по связям.
* **Маршруты событий** . Категория "маршруты событий" содержит API-интерфейсы для [маршрутизации данных](concepts-route-events.md)через систему и в подчиненные службы.

Самая последняя версия API плоскости данных — _**2020-10-31**_.

Чтобы использовать API-интерфейсы плоскости данных, выполните следующие действия.
* Интерфейсы API можно вызывать напрямую, по...
   - Ссылка на последнюю папку Swagger в [репозитории Swagger в плоскости данных](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). В эту папку также входит папка примеров, в которых показано использование. 
   - Просмотр [справочной документации по API](/rest/api/azure-digitaltwins/).
* Вы можете использовать **пакет SDK для .NET (C#)**. Для использования пакета SDK для .NET...
   - Вы можете просмотреть и добавить пакет из NuGet: [Azure. дигиталтвинс. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Вы можете просмотреть [справочную документацию по пакету SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
   - Вы можете найти источник SDK, включая папку примеров, в GitHub: [клиентскую библиотеку Azure IOT Digital двойников для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - подробные сведения и примеры использования можно просмотреть, перейдя к разделу [*SDK .NET (C#) (панель данных)*](#net-c-sdk-data-plane) этой статьи.
* Вы можете использовать **пакет SDK для Java**. Чтобы использовать пакет SDK для Java...
   - Вы можете просмотреть и установить пакет из Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - Вы можете просмотреть [справочную документацию по пакету SDK](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable) .
   - Источник пакета SDK можно найти в GitHub: [Клиентская библиотека Microsoft Azure IOT Digital двойников для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Вы можете использовать **пакет SDK для JavaScript**. Для использования пакета SDK для JavaScript...
   - Вы можете просмотреть и установить пакет из NPM: [Клиентская библиотека Azure Digital двойников Core для JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Вы можете просмотреть [справочную документацию по пакету SDK](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true).
   - Источник пакета SDK можно найти в GitHub: [Клиентская библиотека Azure Digital двойников Core для JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) .
* Вы можете использовать **пакет SDK для Python**. Чтобы использовать пакет SDK для Python...
   - Вы можете просмотреть и установить пакет из PyPi: [Клиентская библиотека Azure Digital двойников Core для Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Вы можете просмотреть [справочную документацию по пакету SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).
   - Источник пакета SDK можно найти в GitHub: [Клиентская библиотека Azure Digital двойников Core для Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Вы можете создать пакет SDK для другого языка с помощью функции автоотдыха. Следуйте инструкциям в разделе [*инструкции по созданию настраиваемых пакетов SDK для Azure Digital двойников с помощью автоrestful*](how-to-create-custom-sdks.md).

Вы также можете использовать API-интерфейсы плоскости с датами, взаимодействуя с Azure Digital двойников через интерфейс [командной строки](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>Пакет SDK для .NET (C#) (плоскость данных)

Пакет SDK Azure Digital двойников .NET (C#) является частью пакета Azure SDK для .NET. Это открытый исходный код, основанный на API-интерфейсах плоскости данных двойников Azure Digital.

> [!NOTE]
> Дополнительные сведения о разработке SDK см. в общих [принципах разработки для пакетов SDK для Azure](https://azure.github.io/azure-sdk/general_introduction.html) и конкретных [рекомендациях по проектированию .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Чтобы использовать пакет SDK, включите в проект пакет NuGet **Azure. дигиталтвинс. Core** . Вам также потребуется последняя версия пакета **Azure. Identity** . В Visual Studio эти пакеты можно добавить с помощью диспетчера пакетов NuGet (доступ к которому осуществляется с помощью *средств > диспетчер пакетов nuget > управления пакетами NuGet для решения*). Кроме того, вы можете использовать средство командной строки .NET с командами, приведенными в следующих ссылках пакета NuGet, чтобы добавить их в проект:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Это пакет для [пакета SDK Azure Digital Twins для .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Эта библиотека предоставляет инструменты для проверки подлинности в Azure.

Подробное пошаговое руководство по использованию API на практике см. в [*руководстве по созданию кода для клиентского приложения*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Примеры использования пакета SDK для .NET

Ниже приведены примеры кода, иллюстрирующие использование пакета SDK для .NET.

Проверка подлинности в службе:

```csharp
// Authenticate against the service and create a client
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
```

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

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
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
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
twinData.Contents.Add("data", "Hello World!");
try {
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("firstTwin", twinData);
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

Вспомогательные функции сериализации, доступные в пакете SDK, позволяют быстро создавать или десериализовать данные двойника для доступа к основным сведениям. Поскольку основные методы пакета SDK возвращают данные двойника в формате JSON по умолчанию, можно использовать эти вспомогательные классы для дальнейшего разбиения данных двойника.

Доступны следующие вспомогательные классы:
* `BasicDigitalTwin`— Представляет основные данные цифрового двойникаа.
* `BasicRelationship`— Представляет основные данные связи.
* `UpdateOperationUtility`— Представляет сведения об исправлении JSON, используемые при вызове Update.
* `WriteableProperty`: Представляет метаданные свойства

##### <a name="deserialize-a-digital-twin"></a>Десериализация цифрового двойника

Вы всегда можете десериализовать данные двойника, используя библиотеку JSON по своему усмотрению, например `System.Test.Json` или `Newtonsoft.Json` . Для базового доступа к двойника вспомогательные классы делают это немного более удобным.

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

`BasicDigitalTwin`Вспомогательный класс также предоставляет доступ к свойствам, определенным в двойника, через `Dictionary<string, object>` . Чтобы вывести список свойств двойника, можно использовать:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
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
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
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
client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

##### <a name="deserialize-a-relationship"></a>Десериализация связи

Вы всегда можете десериализовать данные связи в выбранный вами тип. Для базового доступа к связи используйте тип `BasicRelationship` .

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`Вспомогательный класс также предоставляет доступ к свойствам, определенным для связи, через `IDictionary<string, object>` . Чтобы вывести список свойств, можно использовать:

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Создание связи

С помощью `BasicRelationship` класса можно также подготовить данные для создания связей в экземпляре двойника:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.Properties = props;
client.CreateOrReplaceRelationshipAsync("mySourceTwin", "rel001", rel);
```

##### <a name="create-a-patch-for-twin-update"></a>Создание исправления для обновления двойника

Вызовы Update для двойников и связей используют структуру [исправления JSON](http://jsonpatch.com/) . Чтобы создать списки операций исправления JSON, можно использовать, `JsonPatchDocument` как показано ниже.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", 25.0);
updateTwinData.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
updateTwinData.AppendRemoveOp("/Humidity");

client.UpdateDigitalTwin("myTwin", updateTwinData);
```

## <a name="general-apisdk-usage-notes"></a>Общие примечания об использовании API/SDK

> [!NOTE]
> Обратите внимание, что Azure Digital двойников сейчас не поддерживает **общий доступ к ресурсам между источниками (CORS)**. Дополнительные сведения о стратегиях влияния и разрешения см. в разделе Основные понятия [*общего доступа к ресурсам в разных источниках (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) *: безопасность для решений Digital двойников в Azure*.

В следующем списке приведены дополнительные сведения и общие рекомендации по использованию API и пакетов SDK.

* Вы можете использовать средство HTTP для тестирования ОСТАВШЕЙся работы, например POST, чтобы выполнять прямые вызовы к интерфейсам API цифровых двойников Azure. Дополнительные сведения об этом процессе см. [*в разделе инструкции. Создание запросов с помощью POST*](how-to-use-postman.md).
* Чтобы использовать пакет SDK, создайте экземпляр `DigitalTwinsClient` класса. Конструктору требуются учетные данные, которые можно получить с помощью различных методов проверки подлинности в `Azure.Identity` пакете. Дополнительные сведения `Azure.Identity` см. в [документации по пространству имен](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet). 
* Вы можете найти `InteractiveBrowserCredential` полезную информацию при начале работы, но есть несколько других вариантов, включая учетные данные для [управляемого удостоверения](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), которые, скорее всего, будут использоваться для проверки подлинности [функций Azure, НАСТРОЕНных с помощью MSI](../app-service/overview-managed-identity.md?tabs=dotnet) в Azure Digital двойников. Дополнительные сведения о см `InteractiveBrowserCredential` . в [документации по его классу](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet).
* Все вызовы API служб предоставляются как функции-члены `DigitalTwinsClient` класса.
* Все функции службы существуют в синхронных и асинхронных версиях.
* Все функции службы создают исключение для любого возвращаемого состояния, равного 400 или выше. Убедитесь, что вызовы заносятся в `try` раздел и перехватите по крайней мере `RequestFailedExceptions` . Дополнительные сведения об этом типе исключения см. [здесь](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet).
* Большинство методов службы возвращают `Response<T>` или ( `Task<Response<T>>` для асинхронных вызовов), где `T` — это класс возвращаемого объекта для вызова службы. [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet)Класс инкапсулирует возвращаемые службы и представляет возвращаемые значения в `Value` поле.  
* Методы службы с страничными результатами возвращают `Pageable<T>` или `AsyncPageable<T>` как результаты. Дополнительные сведения о `Pageable<T>` классе см. здесь [](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview). Дополнительные сведения `AsyncPageable<T>` см. [здесь](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview).
* Можно выполнять итерацию по страничным результатам с помощью `await foreach` цикла. Дополнительные сведения об этом процессе см. [здесь](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Базовый пакет SDK — `Azure.Core` . См. [документацию по пространству имен Azure](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview) для получения справки по инфраструктуре и типам пакета SDK.

Методы службы возвращают строго типизированные объекты везде, где это возможно. Однако так как Azure Digital двойников основан на моделях, настраиваемых пользователем во время выполнения (через модели ДТДЛ, загруженные в службу), многие API службы принимают и возвращают данные двойника в формате JSON.

## <a name="monitor-api-metrics"></a>Мониторинг метрик API

Метрики API, такие как запросы, задержка и частота сбоев, можно просмотреть в [портал Azure](https://portal.azure.com/). 

На домашней странице портала найдите свой экземпляр Azure Digital двойников, чтобы получить сведения о нем. Выберите параметр **метрики** в меню экземпляра Azure Digital двойников, чтобы открыть страницу *метрики* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Снимок экрана со страницей &quot;метрики&quot; для Azure Digital двойников":::

Здесь можно просмотреть метрики для своего экземпляра и создать пользовательские представления.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как выполнять прямые запросы к API-интерфейсам с помощью процедуры POST:
* [*Пошаговое руководство. выполнение запросов с помощью POST*](how-to-use-postman.md)

Или попробуйте использовать пакет SDK для .NET, создав клиентское приложение с помощью этого учебника:
* [*Руководство. Написание кода для клиентского приложения*](tutorial-code.md)
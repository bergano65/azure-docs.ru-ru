---
title: Мигрируйте данные лица через подписки - Лицо
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как перенести сохраненные данные лица из одной подписки Face в другую.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169809"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Перенос данных распознавания лиц в другую подписку API распознавания лиц

В этом руководстве показано, как перемещать данные лица, например, сохраненный объект PersonGroup с лицами, в другую подписку Azure Cognitive Services Face. Для перемещения данных используется функция Snapshot. Таким образом, при перемещении или расширении операций вам не приходится постоянно создавать и обучать объект PersonGroup или FaceList. Например, возможно, вы создали объект PersonGroup с помощью бесплатной пробной подписки и теперь хотите перенести его на платную подписку. Или может потребоваться синхронизация данных о лицах между подписями в разных регионах для крупной операции предприятия.

Эта же стратегия переноса применяется к объектам LargePersonGroup и LargeFaceList. Если вы не знакомы с понятиями в этом руководстве, ознакомьтесь с их определениями в руководстве по [распознаванию лиц.](../concepts/face-recognition.md) В этом руководстве используется клиентская библиотека Face .NET с C..

## <a name="prerequisites"></a>Предварительные требования

Вам нужны следующие элементы:

- Два ключа подписки Face, один с существующими данными и один для миграции. Чтобы подписаться на сервис Face и получить ключ, следуйте инструкциям в [учетной записи «Создайте когнитивные услуги».](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- Строка идентификатора подписки Face, соответствующая целевой подписке. Чтобы найти его, выберите **Обзор** на портале Azure. 
- Любое издание [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

В этом руководстве используется простое приложение для запуска миграции данных лица. Для полной реализации см. [образец снимка лица](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) на GitHub.

1. В Visual Studio создайте новое приложение Console.NET Framework project. Назовите его **FaceApiSnapshotSample**.
1. Получите необходимые пакеты NuGet. Нажмите правой кнопкой мыши вашего проекта в Solution Explorer и выберите **Пакеты Управления NuGet.** Выберите вкладку **«Просмотр»** и **выберите Пререлиз «Включите».** Найдите и установите следующий пакет:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview).

## <a name="create-face-clients"></a>Создание клиентов для распознавания лиц

В методе **Main** в файле *Program.cs* создайте два экземпляра [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) для исходной и целевой подписок. В этом примере в качестве источника используется подписка Face в регионе Восточной Азии и подписка на Запад США. В этом примере показано, как прегнетать данные из одного региона Azure в другой. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Заполните значения ключевых значений подписки и URL-адреса конечных точек для исходных и целевых подписок.


## <a name="prepare-a-persongroup-for-migration"></a>Подготовка PersonGroup для переноса

Вам потребуется идентификатор объекта PersonGroup в вашей исходной подписке, чтобы перенести его в целевую подписку. Используйте метод [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) для получения списка объектов PersonGroup. Затем получите недвижимость [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Этот процесс выглядит по-разному в зависимости от того, какие объекты PersonGroup у вас есть. В этом руководстве хранится идентификатор источника PersonGroup `personGroupId`ID.

> [!NOTE]
> [Код образца](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) создает и обучает новую Группу лиц для миграции. В большинстве случаев, вы уже должны иметь PersonGroup для использования.

## <a name="take-a-snapshot-of-a-persongroup"></a>Сделать снимок PersonGroup

Снимок — это временное удаленное хранилище для определенных типов данных Face. Он функционирует как своего рода буфер обмена для копирования данных из одной подписки в другую. Во-первых, вы делаете снимок данных в подписке на исход. Затем вы применяете его к новому объекту данных в целевой подписке.

Используйте экземпляр FaceClient подписки для снимка PersonGroup. Используйте [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) с идентификатором PersonGroup и идентификатором целевой подписки. Если у вас есть несколько целевых подписок, добавьте их в качестве блоки в третьем параметре.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Процесс приема и применения снимков не нарушает регулярных вызовов в исходные группы или целевые группы лиц или FaceLists. Не делайте одновременных вызовов, которые меняют исходный объект, [например, вызовы управления FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) или вызов [PersonGroup Train.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) Операция моментального снимка может работать до или после этих операций или может столкнуться с ошибками.

## <a name="retrieve-the-snapshot-id"></a>Извлечение идентификатора моментального снимка

Метод, используемый для снимков, асинхронный, поэтому необходимо дождаться его завершения. Операции моментального снимка не могут быть отменены. В этом коде `WaitForOperation` метод отслеживает асинхронный вызов. Он проверяет статус каждые 100 мс. После завершения операции извлеките идентификатор `OperationLocation` операции, разогнав поле. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Типичное `OperationLocation` значение выглядит следующим образом:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Вспомогательный метод `WaitForOperation` находится здесь:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

После отображаемого `Succeeded`состояния операции, получите идентификатор моментального момента, разобрав `ResourceLocation` поле возвращенного экземпляра OperationStatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Типичное `resourceLocation` значение выглядит следующим образом:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Приложить снимок к целевой подписке

Затем создайте новую PersonGroup в целевой подписке, используя случайно сгенерированный идентификатор. Затем используйте экземпляр FaceClient целевой подписки, чтобы применить снимок к этой PersonGroup. Передайте идентификатор моментального снимка и новый идентификатор PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Объект моментального снимка действителен только 48 часов. Только если вы собираетесь использовать его для миграции данных вскоре после этого.

Запрос на применение моментального снимка возвращает другой идентификатор операции. Чтобы получить этот идентификатор, разобрать `OperationLocation` поле возвращенного экземпляраSnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Процесс применения моментального снимка также асинхронный, поэтому снова используйте, `WaitForOperation` чтобы дождаться его завершения.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Проверка переноса данных

После применения снимка новая PersonGroup в целевой подписке заполняется исходными данными о лице. По умолчанию результаты обучения также копируются. Новая PersonGroup готова к вызовам идентификации лиц без переподготовки.

Чтобы протестировать миграцию данных, запустите следующие операции и сравните результаты, которые они печатают, с консолью:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Используйте следующие вспомогательные методы:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Теперь вы можете использовать новую PersonGroup в целевой подписке. 

Чтобы обновить целевую группу PersonGroup снова в будущем, создайте новую PersonGroup для получения снимка. Для этого следуйте шагам в этом руководстве. Один объект PersonGroup может иметь снимок, применяемый к нему только один раз.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения миграции данных лица вручную удалите объект моментального снимка.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Дальнейшие действия

Далее ознакомьтесь с соответствующей справочной документацией API, изучите пример приложения, использующего функцию Snapshot, или следуйте руководству по началу использования других операций API, упомянутых здесь:

- [Справочная документация по моментальным снимкам (SDK для .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Образец снимка лица](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Добавление лиц](how-to-add-faces.md)
- [Обнаружение лиц на изображении](HowtoDetectFacesinImage.md)
- [Идентификация лиц на изображении](HowtoIdentifyFacesinImage.md)

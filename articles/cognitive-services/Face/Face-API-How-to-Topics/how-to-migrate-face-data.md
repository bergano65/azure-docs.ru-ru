---
title: Перенос данных распознавания лиц между подписками — API распознавания лиц
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как перенести сохраненные данные распознавания лиц из одной подписки API распознавания лиц в другую.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 02e9b64c89eda1471d644e0116bbf8c1c061ccc3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682521"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Перенос данных распознавания лиц в другую подписку API распознавания лиц

В этом руководстве показано, как переместить данные распознавания лиц (например, сохраненную группу **PersonGroup**) в другую подписку API распознавания лиц с помощью функции моментального снимка. Это позволяет избежать необходимости многократного создания и обучения группы **PersonGroup** или списка **FaceList** при перемещении или расширении операций. Например, вы создали группу **PersonGroup** в бесплатной пробной подписке и теперь хотите перенести ее в платную подписку или синхронизировать данные распознавания лиц между регионами для крупной корпоративной операции.

Эта же стратегия переноса применяется к объектам **LargePersonGroup** и **LargeFaceList**. Если вы не знакомы с основными понятиями в этом руководстве, см. в разделе их определения в [сталкиваются распознавание концепции](../concepts/face-recognition.md) руководства. В этом руководстве используется клиентская библиотека API распознавания лиц для .NET с C#.

## <a name="prerequisites"></a>Технические условия

- Два ключа подписки API распознавания лиц (один с существующими данными и один для выполнения переноса). Следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку на службу API распознавания лиц и свой ключ.
- Строка идентификатора подписки API распознавания лиц, соответствующая целевой подписке (находится в колонке **Обзор** на портале Azure). 
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

В этом руководстве для переноса данных распознавания лиц используется простое консольное приложение. Полную реализацию см. в [примере моментального снимка API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) на GitHub.

1. В Visual Studio создайте проект **Консольное приложение (.NET Framework)** и назовите его **FaceApiSnapshotSample**.
1. Получите необходимые пакеты NuGet. В обозревателе решений щелкните свой проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. Нажмите кнопку **Обзор** и выберите **Включить предварительные выпуски**, а затем найдите и установите следующий пакет:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview).

## <a name="create-face-clients"></a>Создание клиентов для распознавания лиц

В методе **Main** в файле *Program.cs* создайте два экземпляра **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** для исходной и целевой подписок. В этом примере мы будем использовать подписку API распознавания лиц в регионе "Восточная Азия" в качестве источника и подписку в регионе "Западная часть США" в качестве целевого объекта. Пример продемонстрирует, как переносить данные из одного региона Azure в другой. Если ваши подписки находятся в разных регионах, необходимо изменить строки `Endpoint`.

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

Нужно заполнить значения ключей подписок и URL-адресов конечных точек для исходной и целевой подписок.


## <a name="prepare-a-persongroup-for-migration"></a>Подготовка PersonGroup для переноса

Вам потребуется идентификатор объекта **PersonGroup** в вашей исходной подписке, чтобы перенести его в целевую подписку. Используйте метод **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)**, чтобы получить список объектов **PersonGroup**, а затем получите свойство **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)**. Этот процесс будет выглядеть по-разному в зависимости от того, какие объекты **PersonGroup** у вас есть. В этом руководстве идентификатор **PersonGroup** в исходной подписке хранится в `personGroupId`.

> [!NOTE]
> [Пример кода](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) создает и обучает новый объект **PersonGroup** для переноса, но в большинстве случаев у вас уже должен быть объект **PersonGroup**, который нужно использовать.

## <a name="take-snapshot-of-persongroup"></a>Создание моментального снимка объекта PersonGroup

Моментальный снимок является временным удаленным хранилищем для определенных типов данных API распознавания лиц. Он функционирует как своего рода буфер обмена для копирования данных из одной подписки в другую. Сначала пользователь "делает" моментальный снимок данных в исходной подписке, а затем "применяет" его к новому объекту данных в целевой подписке.

Используйте экземпляр **FaceClient** в исходной подписке, чтобы сделать моментальный снимок **PersonGroup** с помощью **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** с идентификаторами **PersonGroup** и целевой подписки. При наличии нескольких целевых подписок их можно добавить в качестве записей массива в третьем параметре.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Процесс создания и применения моментальных снимков не прерывает обычных вызовов к исходным или целевым объектам **PersonGroup** (или **FaceList**). Тем не менее, мы не рекомендуем всего одновременных вызовов, изменить исходный объект ([вызовы управления FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) или [Train каждой группе людей](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) вызвать, например), так как операция snapshot может выполнение до или после выполнения этих операций или могут возникнуть ошибки.

## <a name="retrieve-the-snapshot-id"></a>Получение идентификатора моментального снимка

Использование метода моментального снимка является асинхронным, поэтому вам потребуется дождаться его завершения (моментальный снимок, который не может быть отменен операций). В этом коде метод `WaitForOperation` отслеживает асинхронный вызов, проверяя состояние каждые 100 мс. После завершения операции можно будет получить ее идентификатор. Для этого нужно выполнить синтаксический анализ поля `OperationLocation`. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Стандартное значение `OperationLocation` будет выглядеть следующим образом:

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

Если состояние операции помечено как `Succeeded`, вы можете получить идентификатор моментального снимка, проанализировав поле `ResourceLocation` возвращенного экземпляра **OperationStatus**.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Стандартное значение `resourceLocation` будет выглядеть следующим образом:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Применение моментального снимка к целевой подписке

Затем создайте новый объект **PersonGroup** в целевой подписке, используя случайно сгенерированный идентификатор. Потом с помощью экземпляра **FaceClient** целевой подписки примените моментальный снимок к этому объекту PersonGroup, передав идентификаторы моментального снимка и нового объекта **PersonGroup**. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Объект моментального снимка действителен только в течение 48 часов. Моментальный снимок следует делать только в том случае, если вскоре он будет использоваться для переноса данных.

Запрос на применение моментального снимка вернет идентификатор другой операции. Этот идентификатор можно получить путем синтаксического анализа поля `OperationLocation` возвращенного экземпляра **applySnapshotResult**. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Процесс приложения создания моментальных снимков также является асинхронным, поэтому снова используйте `WaitForOperation`, чтобы дождаться его завершения.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Проверка переноса данных

После применения моментального снимка новый объект **PersonGroup** в целевой подписке должен быть заполнен исходными данными распознавания лиц. По умолчанию результаты обучения также копируются, поэтому новый объект **PersonGroup** будет готов к вызовам идентификации лиц без необходимости повторного обучения.

Для проверки переноса данных можно выполнить следующие операции и сравнить результаты, которые отображаются в консоли.

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

Теперь можно приступить к использованию нового объекта **PersonGroup** в целевой подписке. 

Если вы снова захотите обновить целевой объект **PersonGroup**, вам нужно будет создать новый объект **PersonGroup** (следуя инструкциям в этом руководстве), чтобы получить моментальный снимок. К одному объекту **PersonGroup** можно применить только один моментальный снимок.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения переноса данных распознавания лиц рекомендуется вручную удалить объект моментального снимка.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Дальнейшие действия

Далее см. в разделе соответствующей справочную документацию по API, изучите пример приложения, использующего функции создания моментальных снимков или выполните руководство по использованию других операций API, упомянутые здесь.

- [Справочная документация по моментальным снимкам (SDK для .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Пример моментального снимка API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Пример: как обнаруживать лица на изображении](how-to-add-faces.md)
- [Практическое руководство по обнаружению лиц на изображении](HowtoDetectFacesinImage.md)
- [Пример: как идентифицировать лица на изображениях](HowtoIdentifyFacesinImage.md)

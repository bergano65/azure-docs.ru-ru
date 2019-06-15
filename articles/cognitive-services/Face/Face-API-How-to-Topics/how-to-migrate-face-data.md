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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65913790"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Перенос данных распознавания лиц в другую подписку API распознавания лиц

В этом руководстве показано, как переместить данные лиц, такие как сохраненный объект каждой группе людей с лиц, в другую подписку API распознавания лиц Cognitive Services Azure. Чтобы переместить данные, используйте функции создания моментальных снимков. Таким образом вам не потребуется повторно создать и обучить объект каждой группе людей или FaceList, при перемещении или разверните ваши операции. Например возможно вы создал объект каждой группе людей, с помощью бесплатной пробной подписки и теперь требуется перенести его для работы с платной подпиской. Или может потребоваться синхронизация данных лиц в регионах для крупной операции.

Этот же стратегии миграции также применяется к объектам LargePersonGroup и LargeFaceList. Если вы не знакомы с понятиями в этом руководстве, см. в разделе их определения в [сталкиваются распознавание концепции](../concepts/face-recognition.md) руководства. В этом руководстве используется клиентская библиотека API распознавания лиц для .NET с C#.

## <a name="prerequisites"></a>Технические условия

Вам потребуются следующие компоненты:

- Два API распознавания лиц ключи подписки, один с существующими данными и один для миграции. Чтобы подписаться на службу API распознавания лиц и получите ключ, следуйте инструкциям в [создать учетную запись Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Строка идентификатора подписки API распознавания лиц, соответствующий целевой подписки. Чтобы найти его, выберите **Обзор** на портале Azure. 
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

В этом руководстве используется простое консольное приложение для выполнения переноса данных лиц. Полная реализация, см. в разделе [snapshot, образец API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) на сайте GitHub.

1. В Visual Studio создайте проект консольного приложения .NET Framework. Назовите его **FaceApiSnapshotSample**.
1. Получите необходимые пакеты NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите **управление пакетами NuGet**. Выберите **Обзор** и выберите **включить предварительные выпуски**. Найти и установить следующий пакет:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview).

## <a name="create-face-clients"></a>Создание клиентов для распознавания лиц

В **Main** метод в *Program.cs*, создайте две [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) экземпляров для ваших исходной и целевой подписок. В этом примере использует Face подписки в регионе "Восточная Азия" как источником и Западной части США подписки в качестве целевой. В этом примере показано, как перенести данные из одного региона Azure в другую. Если ваши подписки в разных регионах, измените `Endpoint` строк.

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

Заполните значения ключа подписки и URL-адреса конечной точки для вашей исходной и целевой подписках.


## <a name="prepare-a-persongroup-for-migration"></a>Подготовка PersonGroup для переноса

Требуется идентификатор каждой группе людей в вашей подписке источника для миграции в целевой подписке. Используйте [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) метод для извлечения списка объектов в каждой группе людей. Затем получите [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) свойство. Этот процесс выглядит по-разному в зависимости от какие каждой группе людей объекты, у вас есть. В этом руководстве источник каждой группе людей идентификатор хранится в `personGroupId`.

> [!NOTE]
> [Пример кода](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) создают и обучают новый каждой группе людей для переноса. В большинстве случаев необходимо иметь каждой группе людей, для использования.

## <a name="take-a-snapshot-of-a-persongroup"></a>Создайте моментальный снимок каждой группе людей

Моментальный снимок — временного удаленного хранилища для определенных типов данных лиц. Он функционирует как своего рода буфер обмена для копирования данных из одной подписки в другую. Во-первых сделать снимок данных в исходной подписке. Затем применить его к объекту данных в целевой подписке.

Используйте экземпляр FaceClient исходной подписки, чтобы создать моментальный снимок каждой группе людей. Используйте [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) с Идентификатором каждой группе людей, а также идентификатор целевой подписки. Если у вас несколько подписок целевой, добавьте их как массив записей в третий параметр.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Процесс получения и применение моментальных снимков не нарушить работу каких-либо регулярное вызовов источника или целевой установлены или списке лиц. Не вносите одновременные вызовы, изменяющие исходного объекта, такие как [вызовы управления FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) или [Train каждой группе людей](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) вызове, к примеру. Операция snapshot может выполняться до или после выполнения этих операций или могут возникнуть ошибки.

## <a name="retrieve-the-snapshot-id"></a>Получить идентификатор моментального снимка

Метод, используемый для создания моментальных снимков является асинхронным, поэтому необходимо дождаться его завершения. Операции с моментальными снимками нельзя отменить. В этом коде `WaitForOperation` метод наблюдает асинхронного вызова. Он проверяет состояние каждой 100 мс. После завершения операции, получение идентификатора операции путем синтаксического анализа `OperationLocation` поля. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Типичный `OperationLocation` значение выглядит следующим образом:

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

После состояние операции отображается `Succeeded`, получите идентификатор моментального снимка путем синтаксического анализа `ResourceLocation` поле возвращаемого OperationStatus экземпляра.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Типичный `resourceLocation` значение выглядит следующим образом:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Применение моментального снимка в целевую подписку

Создайте новый каждой группе людей в целевой подписке с помощью случайным образом созданным идентификатором. Целевая подписка FaceClient экземпляр используйте для применения моментального снимка для этой каждой группе людей. Передачи в моментальном снимке, идентификатора и новый идентификатор каждой группе людей.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Моментальный снимок объекта действителен в течение всего 48 часов. Только создать моментальный снимок, если вы планируете использовать его для переноса данных вскоре после.

Применить моментальный снимок запрос возвращает другой идентификатор операции. Чтобы получить этот идентификатор, синтаксический анализ `OperationLocation` поле экземпляра, возвращенного applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Процесс создания моментального снимка приложения также является асинхронным, поэтому снова использовать `WaitForOperation` дождаться его завершения.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Проверка переноса данных

После применения моментального снимка, исходные данные лиц заполняет новый каждой группе людей в целевой подписке. По умолчанию результаты обучения также копируются. Новый каждой группе людей готов для вызовов Идентификация лиц без необходимости повторного обучения.

Чтобы протестировать перенос данных, выполните следующие операции и сравните результаты, которые выводят в консоль:

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

Теперь можно использовать новый каждой группе людей в целевой подписке. 

Чтобы обновить целевой объект каждой группе людей в будущем, создайте новый каждой группе людей для получения снимка. Чтобы сделать это, выполните действия, описанные в этом руководстве. Один объект каждой группе людей может иметь моментальный снимок применен только один раз.

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив миграцию данных лиц, вручную удалите объект снимка.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Дальнейшие действия

Далее см. в разделе соответствующей справочную документацию по API, изучите пример приложения, использующего функции создания моментальных снимков или выполните руководство по использованию других операций API, упомянутые здесь:

- [Справочная документация по моментальным снимкам (SDK для .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Snapshot, образец API распознавания лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Добавление лиц](how-to-add-faces.md)
- [Определение лиц на изображении](HowtoDetectFacesinImage.md)
- [Идентификация лиц на изображении](HowtoIdentifyFacesinImage.md)

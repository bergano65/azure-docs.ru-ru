---
title: Подрезать видео при кодировании со службами мультимедиа Azure
description: В этом разделе описывается, как подрезать видео при кодировании со службами мультимедиа Azure с помощью пакета SDK для .NET
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: be39c9390dacf6ae0fb44157f6185c5cf17a8725
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021227"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Подрезать видео при кодировании с помощью служб мультимедиа — .NET

Вы можете обрезать или подрезать видео при его кодировании с помощью [задания](/rest/api/media/jobs). Эта функция работает с любыми [преобразованиями](/rest/api/media/transforms), созданными с помощью предустановок [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) или [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset).

В следующем примере кода C# создается задание, которое обрезает видео в ресурсе при отправке задания кодирования. 

## <a name="prerequisites"></a>Обязательные условия

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- [Создание учетной записи служб мультимедиа Azure](./create-account-howto.md)
- Создание преобразования и входных и выходных ресурсов. Вы можете узнать, как создать преобразование и входные и выходные ресурсы в учебнике [Отправка, кодирование и потоковая передача с помощью .NET](stream-files-tutorial-with-api.md) .
- Ознакомьтесь с разделом [концепция кодирования](encoding-concept.md) .

## <a name="example"></a>Пример

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Дальнейшие действия

[Кодирование с помощью пользовательского преобразования](customize-encoder-presets-how-to.md) 

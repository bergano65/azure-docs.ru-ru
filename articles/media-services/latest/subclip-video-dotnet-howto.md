---
title: Subclip видео при кодировании с Azure Media Services
description: В этой теме описывается, как подстегивать видео при кодировании с помощью Медиа-службы Azure с помощью .NET SDK
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
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305039"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Subclip видео при кодировании со средствами массовой информации - .NET

Вы можете обрезать или подстричь видео при кодировании его с помощью [работы.](https://docs.microsoft.com/rest/api/media/jobs) Эта функциональность работает [Transform](https://docs.microsoft.com/rest/api/media/transforms) с любым преобразованием, который построен с использованием либо presets [builtInStandardset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) либо пресетов [StandardEnCoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Следующий пример C's создает задание, которое обрезает видео в активе при отправке задания кодирования. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- [Создайте учетную запись служб мультимедиа Azure](create-account-cli-how-to.md).
- Создание трансформаторных и входных и выходных активов. Вы можете увидеть, как создать преобразование и ввод и выходные активы в [загрузке, кодировать и поток видео с помощью .NET](stream-files-tutorial-with-api.md) учебник.
- Просмотрите тему [концепции кодирования.](encoding-concept.md)

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

[Как закодировать с помощью пользовательского преобразования](customize-encoder-presets-how-to.md) 

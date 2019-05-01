---
title: Задержка LiveEvent в Службах мультимедиа Azure | Документация Майкрософт
description: В этом разделе представлены общие сведения о задержке LiveEvent и показано, как настроить ее низкое значение.
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
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702785"
---
# <a name="live-event-latency-in-media-services"></a>Задержка события потоковой трансляции в Службах мультимедиа

В этой статье показано, как настроить низкую задержку [события потоковой трансляции](https://docs.microsoft.com/rest/api/media/liveevents). Здесь также рассмотрены результаты настройки низкой задержки в разных проигрывателях. Результаты различаются в зависимости от задержки сети и CDN.

Чтобы использовать новую функцию **LowLatency**, настройте для параметра **StreamOptionsFlag** значение **LowLatency** в **LiveEvent**. При создании [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) для воспроизведения HLS задайте для [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) значение 1. Когда поток запущен и работает, можно использовать демонстрационную страницу [проигрывателя мультимедиа Azure](https://ampdemo.azureedge.net/) (AMP) и установить параметры воспроизведения так, чтобы использовался профиль Low Latency Heuristics Profile (Эвристический профиль малой задержки).

> [!NOTE]
> В настоящее время LowLatency HeuristicProfile в проигрывателе мультимедиа Azure предназначен для воспроизведения потоков в протоколе MPEG-DASH, CSF или CMAF формате (например, `format=mdp-time-csf` или `format=mdp-time-cmaf`). 

В следующем примере .NET показано, как задать **LowLatency** в **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Полный пример см. здесь: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Задержка событий потоковой трансляции

В следующих таблицах приведены результаты настройки задержки (установленный флаг LowLatency) в Службах мультимедиа. Задержка измеряется с момента, когда веб-канал входного потока достигает службы, и до момента, когда проигрыватель может запросить воспроизведение. Чтобы оптимально использовать низкую задержку, в параметрах кодировщика задайте для длительности группы изображений (GOP) значение менее 1 секунды. Если задать меньшую длительность GOP, вы уменьшите использование пропускной способности и скорость при одинаковой частоте кадров. Это особенно подходит для видео, где меньше движения.

### <a name="pass-through"></a>Сквозной режим 

||Включена двухсекундная GOP с малой задержкой|Включена односекундная GOP с малой задержкой|
|---|---|---|
|DASH в AMP|10 с|8 с|
|HLS на собственном проигрывателе iOS|14 с|10 с|

### <a name="live-encoding"></a>Кодирование в реальном времени

||Включена двухсекундная GOP с малой задержкой|Включена односекундная GOP с малой задержкой|
|---|---|---|
|DASH в AMP|14 с|10 с|
|HLS на собственном проигрывателе iOS|18 с|13 с|

> [!NOTE]
> Значение сквозной задержки может зависеть от условий локальной сети или наличия уровня кэширования CDN. Следует протестировать используемые конфигурации.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о потоковой трансляции](live-streaming-overview.md)
- [Руководство по потоковой трансляции](stream-live-tutorial-with-api.md)


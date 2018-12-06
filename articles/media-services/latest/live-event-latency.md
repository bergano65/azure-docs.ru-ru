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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619828"
---
# <a name="liveevent-latency-in-media-services"></a>Задержка LiveEvent в Службах мультимедиа

В этой статье показано, как настроить низкую задержку **LiveEvent**. Здесь также рассмотрены результаты настройки низкой задержки в разных проигрывателях. Результаты различаются в зависимости от задержки сети и CDN. 

Чтобы использовать новую функцию **LowLatency**, настройте для параметра **StreamOptionsFlag** значение **LowLatency** в **LiveEvent**. Когда поток запущен и работает, можно использовать демонстрационную страницу [Проигрывателя мультимедиа Azure](http://ampdemo.azureedge.net/) (AMP) и установить параметры воспроизведения так, чтобы использовался Low Latency Heuristics Profile (Эвристический профиль малой задержки).

В следующем примере .NET показано, как задать **LowLatency** в **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Полный пример см. здесь: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Сквозная задержка LiveEvents

В следующей таблице приведены результаты настройки задержки (установленный флаг LowLatency) в Службах мультимедиа. Задержка измеряется с момента, когда веб-канал входного потока достигает службы, и до момента, когда игрок может запросить воспроизведение.

||Включена двухсекундная GOP с малой задержкой|Включена односекундная GOP с малой задержкой|
|---|---|---|
|DASH в AMP|10 с|8 с|
|HLS на собственном проигрывателе iOS|14 с|10 с|

> [!NOTE]
> Значение сквозной задержки может зависеть от условий локальной сети или наличия уровня кэширования CDN. Следует протестировать используемые конфигурации.

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения о потоковой трансляции](live-streaming-overview.md)
- [Руководство по потоковой трансляции](stream-live-tutorial-with-api.md)


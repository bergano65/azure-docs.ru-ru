---
title: Переход от Windows Azure Media Encoder к стандарту медиа-кодаре (ru) Документы Майкрософт
description: В этой теме обсуждается, как перейти от мультимедиа-кодареaz Azure media Encoder к медиапроцессору Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513207"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Переход от Windows Azure Media Encoder к стандарту медиа-кодаре

В этой статье рассматриваются шаги для перехода от устаревшего медиапроцессора Windows Azure Media Encoder (WAME) (который в настоящее время отменяется) к медиапроцессору Media Encoder Standard. Для даты выхода на [legacy components](legacy-components.md) пенсию см.

При кодировании файлов с WAME клиенты обычно использовали `H264 Adaptive Bitrate MP4 Set 1080p`именованную предустановленную строку, такую как. Для того, чтобы мигрировать, ваш код должен быть обновлен, чтобы использовать **медиа-процессор Encoder** `H264 Multiple Bitrate 1080p`Standard вместо WAME, и один из эквивалентных [пресетов системы,](media-services-mes-presets-overview.md) как. 

## <a name="migrating-to-media-encoder-standard"></a>Миграция к медиа-стандарту

Вот типичный образец кода СЗ, в который используется устаревший компонент. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Вот обновленная версия, которая использует Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Сложные сценарии 

Если вы создали свой собственный предустановленный кодирование для WAME, используя его схему, есть [эквивалентная схема для Media Encoder Standard.](media-services-mes-schema.md)

## <a name="known-differences"></a>Известные различия 

Media Encoder Standard является более надежным, надежным, имеет более высокую производительность и обеспечивает более высокое качество продукции, чем устаревший кодеер WAME. В дополнение: 

* Media Encoder Standard производит выходные файлы с другой конвенцией именования, чем WAME.
* Media Encoder Standard производит артефакты, такие как файлы, содержащие [метаданные ввода и](media-services-input-metadata-schema.md) [метаданные вывода (s)](media-services-output-metadata-schema.md).
* Как описано на [странице ценообразования](https://azure.microsoft.com/pricing/details/media-services/#encoding) (особенно в разделе часто задаваемые вопросы), при кодировании видео с помощью Media Encoder Standard вы получаете счет в зависимости от продолжительности файлов, производимых в виде вывода. С WAME выставляется счет в зависимости от размеров вхотворного видеофайла (ы) и вывода видеофайла (ы).

## <a name="need-help"></a>Требуется помощь?

Вы можете открыть билет поддержки, перенаправившись в [Новый запрос поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Дальнейшие действия

* [Устаревшие компоненты](legacy-components.md)
* [Страница цен](https://azure.microsoft.com/pricing/details/media-services/#encoding)

---
title: Миграция из Indexer v1 и v2 в Видеоиндекс Медиа-сервисов Azure (ru) Документы Майкрософт
description: В этой теме обсуждается вопрос о том, как перейти от Azure Media Indexer v1 и v2 к видеоиндексу медиасервисов Azure.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513241"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Переход от медиа-индекса и медиа-индекса 2 к индексу видео

В настоящее время отходят от производства [мультипроцессоров Azure Media Indexer](media-services-index-content.md) и [мультипроцессоров Azure Media Indexer 2 Preview.](media-services-process-content-with-indexer2.md) Для даты выхода на [legacy components](legacy-components.md) пенсию см. [Видеоиндекс Медиа Службы Мультимедиа](https://docs.microsoft.com/azure/media-services/video-indexer/) заменяет устаревшие медиапроцессоры.

Video Indexer Мультимедиа Создан на базе Azure Media Analytics, Azure Cognitive Search, Cognitive Services (таких как Face API, Microsoft Translator, API компьютерного зрения и служба пользовательских речевых данных). Индексатор видео позволяет извлекать аналитические сведения из видеоматериалов с помощью видео- и аудиомоделей. Чтобы увидеть, в каких сценариях можно использовать Video Indexer, какие функции он предлагает и как начать работу, смотрите [видео-индексатор видео и аудио-модели.](../video-indexer/video-indexer-overview.md) 

Вы можете извлечь информацию из видео- и аудиофайлов с помощью [пресетов анализатора Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md) или непосредственно с помощью [AIS Video Indexer.](https://api-portal.videoindexer.ai/) В настоящее время существует совпадение между функциями, предлагаемыми AIS Video Indexer и AA Media Services v3.

> [!NOTE]
> Чтобы определить, в каких случаях вам лучше использовать Индексатор видео, а в каких — предустановки анализатора Служб мультимедиа, ознакомьтесь со статьей, в которой приводится [сравнение](../video-indexer/compare-video-indexer-with-media-services-presets.md) этих инструментов. 

В этой статье рассматриваются шаги для перехода от индекса мультимедиа Azure Media Indexer и Azure Media Indexer 2 к видеоиндексу медиаслужб Ызуротаций.  

## <a name="migration-options"></a>Варианты переноса 

|Если вам требуется  |а затем — |
|---|---|
|решение, обеспечивающее транскрипцию от речи к тексту для любого формата медиафайлов в закрытых форматах файлов субтитров: VTT, SRT или TTML<br/>а также дополнительные аудио идеи, такие как: ключевые слова, тема вывод, акустические события, динамика динамики, сущности извлечения и перевода| обновляйте приложения, чтобы использовать возможности Azure Video Indexer с помощью API Video Indexer v2 REST или предустановленного аудиоанализа Azure Media Services v3.|
|возможности от речи к тексту| использовать API речи когнитивных служб напрямую.|  

## <a name="getting-started-with-video-indexer"></a>Начало работы с видео индексом

В следующем разделе вы указываете на соответствующие ссылки: [Как начать работу с Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Начало работы с Медиа-услуг v3 AIS

Azure Media Services v3 API позволяет извлекать сведения из видео- и аудиофайлов через [предсеты анализатора Azure Media Services v3.](../latest/analyzing-video-audio-files-concept.md) 

**AudioAnalyzerPreset** позволяет извлекать множество звуковых аналитических сведений из аудио- или видеофайла. Выход включает в себя файл VTT или TTML для аудиостенограммы и файл JSON (со всеми дополнительными аудио идеи). Аудио идеи включают ключевые слова, индексирование динамиков, и анализ настроений речи. AudioAnalyzerPreset также поддерживает обнаружение языка для определенных языков. Для получения подробной [информации см.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)

### <a name="get-started"></a>Начало работы

Чтобы приступить к работе, изучите следующую статью:

* [Учебник](../latest/analyze-videos-tutorial-with-api.md)
* Образцы AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) или [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Образцы VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) или [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Начало работы с службами речи Cognitive Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) предоставляет услугу передачи речи в текст, которая транскрибирует аудиопотоки в текст в режиме реального времени, которые ваши приложения, инструменты или устройства могут потреблять или отображать. Вы можете использовать речевой текст для [настройки собственной акустической модели, языковой модели или модели произношения.](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md) Для получения дополнительной [Cognitive Services speech-to-text](../../cognitive-services/speech-service/speech-to-text.md)информации см. 

> [!NOTE] 
> Служба речевого текста не принимает форматы видеофайлов и принимает только [определенные аудио форматы.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

Для получения дополнительной информации о тексте к речи службы и как начать работу, смотрите [Что такое речь к тексту?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Известные отличия от унитазанных услуг 

Вы обнаружите, что Video Indexer, Azure Media Services v3 AudioAnalyzerPreset и услуги cognitive Services Speech Services являются более надежными и позволяют повысить качество производства, чем вышедшие в отставку процессоры Azure Media Indexer 1 и Azure Media Indexer 2.  

Некоторые известные различия включают в себя: 

* Службы речи Cognitive Services не поддерживают извлечение ключевых слов. Тем не менее, Video Indexer и Media Services v3 AudioAnalyzerPreset предлагают более надежный набор ключевых слов в формате файлов JSON. 

## <a name="need-help"></a>Требуется помощь?

Вы можете открыть билет поддержки, перенаправившись в [Новый запрос поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Дальнейшие действия

* [Устаревшие компоненты](legacy-components.md)
* [Страница цен](https://azure.microsoft.com/pricing/details/media-services/#encoding)



---
title: Обзор динамической упаковки служб мультимедиа Azure | Документация Майкрософт
description: Этот раздел содержит общие сведения о динамической упаковки.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 4b4f2ec779c37f78b371c27df80c354eccb41e7a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869434"
---
# <a name="dynamic-packaging"></a>Динамическая упаковка

> [!div class="op_single_selector" title1="Выберите версию служб мультимедиа, в которых используется:"]
> * [Версия 3](../latest/dynamic-packaging-overview.md)
> * [Версия 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> В Cлужбы мультимедиа версии 2 больше не добавляются новые компоненты или функциональные возможности. <br/>Ознакомьтесь с новейшей версией Служб мультимедиа — [версией 3](https://docs.microsoft.com/azure/media-services/latest/). Кроме того, см. в разделе [руководство по миграции из версии 2 версии 3](../latest/migrate-from-v2-to-v3.md)

Службы мультимедиа Microsoft Azure можно использовать для передачи содержимого исходных файлов, потоков мультимедиа и защищенного содержимого в различных форматах на вход клиентов, реализованных на базе различных технологий (например, iOS, XBOX, Silverlight или Windows 8). Эти клиенты работают по разным протоколам: например, для iOS используется формат потоковой трансляции HTTP (HLS) V4, а для технологий Silverlight и Xbox необходимо использовать формат Smooth Streaming. Если у вас есть набор файлов MP4 (ISO Base Media 14496-12) с адаптивной скоростью (мультискоростных) или аналогичных файлов Smooth Streaming, которые вам нужно передать клиентам, принимающим форматы MPEG DASH, HLS или Smooth Streaming, вы можете воспользоваться функциями динамической упаковки служб мультимедиа.

С помощью динамической упаковки вам потребуется — создать актив, содержащий набор мультискоростных MP4-файлов или файлов Smooth Streaming с переменной скоростью. Затем с учетом формата, указанного в манифесте или запросе фрагмента, сервер потоковой передачи по запросу организует передачу содержимого по выбранному протоколу. В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента.

На схеме ниже показан рабочий процесс, в котором используются традиционные технологии кодирования и статической упаковки.

![Статическое кодирование](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

На схеме ниже показан рабочий процесс, в котором используются технологии динамической упаковки.

![Динамическое кодирование](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Стандартный сценарий

1. Отправьте входной файл (он также называется мезонинным). Например, это может быть файл в формате H.264, MP4 или WMV (список поддерживаемых форматов см. в статье [Форматы и кодеки стандартного кодировщика служб мультимедиа](media-services-media-encoder-standard-formats.md)).
2. Преобразуйте мезонинный файл в набор мультискоростных MP4-файлов в формате H.264.
3. Опубликуйте ресурс, содержащий набор мультискоростных MP4-файлов, создав указатель OnDemand.
4. Создайте URL-адреса для доступа к содержимому и его потоковой передачи.

## <a name="preparing-assets-for-dynamic-streaming"></a>Подготовка ресурсов для динамической потоковой передачи

Для подготовки актива для динамической потоковой передачи, у вас есть следующие параметры:

- [Отправьте главный файл](media-services-dotnet-upload-files.md).
- [Сформируйте наборы мультискоростных MP4-файлов в формате H.264 с помощью стандартного кодировщика служб мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Выполните потоковую передачу содержимого](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Аудиокодеки, поддерживаемые для динамической упаковки

Динамическое упаковывание поддерживает MP4-файлов, которые содержат аудио, закодированные с [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 или E-AC3), Dolby Atmos или [службDTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR служб DTS, HD служб DTS, HD служб DTS без потери данных). Потоковая передача содержимого Dolby Atmos поддерживается стандартов, как протокол MPEG-DASH с общий формат потоковой передачи (CSF) или формате распространенных приложений мультимедиа (CMAF) фрагментированный MP4, а через HTTP Live Streaming (HLS) с CMAF.

> [!NOTE]
> Для динамической упаковки не поддерживаются файлы с аудиоданными в формате [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (это устаревший кодек).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


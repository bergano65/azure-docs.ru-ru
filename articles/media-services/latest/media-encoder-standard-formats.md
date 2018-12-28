---
title: Форматы и кодеки Media Encoder Standard — Azure
description: В этом разделе приведен обзор кодеков и форматов стандартного кодировщика мультимедиа.
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
ms.date: 12/12/2018
ms.author: juliako;anilmur
ms.openlocfilehash: 22603de40e2ebafb1354377898f5a0432e4f6129
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388590"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Форматы и кодеки стандартного кодировщика служб мультимедиа

В этой статье содержится список наиболее распространенных форматов для импорта и экспорта файлов, которые можно использовать с [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Сведения о создании настраиваемых предустановок с помощью **StandardEncoderPreset** см. в разделе [Создание преобразования с помощью настраиваемой предустановки](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов

| Форматы файлов (расширения файлов) | Поддерживаются |
| --- | --- | --- | --- |
| FLV (с кодеками H.264 и AAC) (.flv) |Yes |
| MXF (.mxf) |Yes |
| GXF (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Yes |
| AVI (без сжатия 8 бит/10 бит) (.avi) |Yes |
| MP4 (MP4, M4A, M4V) и ISMV (ISMA, ISMV) |Yes |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

> [!NOTE]
> Выше приведен список чаще всего встречающихся файловых расширений. Стандартный кодировщик служб мультимедиа поддерживает многие другие стандарты (например, M2TS, MPEG2VIDEO, QT). Если при попытке закодировать файл появляется сообщение о том, что формат не поддерживается, оставьте свой отзыв [здесь](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Форматы аудио во входных контейнерах
Стандарт Media Encoder поддерживает следующие форматы аудио во входных контейнерах:

* файлы MXF, GXF и QuickTime со звуковыми дорожками с чередованием стерео или форматом 5.1;

или

* файлы MXF, GXF и QuickTime, где звук переносится как отдельные дорожки PCM, но сопоставление каналов (стерео или 5.1) можно определить по метаданным файла.

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Поддерживаются |
| --- | --- | --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |
| Avid DNxHD (в MXF) |Yes |
| DVCPro/DVCProHD (в MXF) |Yes |
| Цифровое видео (DV) (в AVI-файлах) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |Нет  |
| MPEG-4, часть 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 без сжатия или мезонинный файл |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H.265| Профиль Main|

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Поддерживаются |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |
| Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptive multi-rate) |Yes |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет  |
| Dolby® E |Нет  |
| Dolby® Digital (AC3) |Нет  |
| Dolby® Digital Plus (E-AC3) |Нет  |

## <a name="output-formats-and-codecs"></a>Выходные форматы и кодеки
В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.

| Формат файла | Видеокодек | Аудиокодек |
| --- | --- | --- |
| MP4  <br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Дополнительная информация

[Создание преобразования с помощью настраиваемой предустановки](customize-encoder-presets-how-to.md)

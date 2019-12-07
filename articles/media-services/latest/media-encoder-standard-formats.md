---
title: Стандартные форматы и кодеки кодировщика — Azure
description: Эта статья содержит список наиболее распространенных форматов файлов импорта и экспорта, которые можно использовать с Стандарденкодерпресет.
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888076"
---
# <a name="standard-encoder-formats-and-codecs"></a>Стандартные форматы и кодеки кодировщика

В этой статье содержится список наиболее распространенных форматов для импорта и экспорта файлов, которые можно использовать с [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Сведения о создании настраиваемых предустановок с помощью **StandardEncoderPreset** см. в разделе [Создание преобразования с помощью настраиваемой предустановки](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов

| Форматы файлов (расширения файлов) | Поддерживается |
| --- | --- |
| FLV (с кодеками H.264 и AAC) (.flv) |ДА |
| MXF (.mxf) |ДА |
| GXF (.gxf) |ДА |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |ДА |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |ДА |
| AVI (без сжатия 8 бит/10 бит) (.avi) |ДА |
| MP4 (MP4, M4A, M4V) и ISMV (ISMA, ISMV) |ДА |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |ДА |
| Matroska/WebM (.mkv) |ДА |
| WAVE/WAV (.wav) |ДА |
| QuickTime (.mov) |ДА |

### <a name="audio-formats-in-input-containers"></a>Форматы аудио во входных контейнерах

Стандартный кодировщик поддерживает следующие звуковые форматы во входных контейнерах:

* файлы MXF, GXF и QuickTime со звуковыми дорожками с чередованием стерео или форматом 5.1;

или

* файлы MXF, GXF и QuickTime, где звук переносится как отдельные дорожки PCM, но сопоставление каналов (стерео или 5.1) можно определить по метаданным файла.

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Поддерживается |
| --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |
| Avid DNxHD (в MXF) |ДА |
| DVCPro/DVCProHD (в MXF) |ДА |
| Цифровое видео (DV) (в AVI-файлах) |ДА |
| JPEG 2000 |ДА |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |
| MPEG-1 |ДА |
| VC-1/WMV9 |ДА |
| Canopus HQ/HQX |Нет |
| MPEG-4, часть 2 |ДА |
| [Theora](https://en.wikipedia.org/wiki/Theora) |ДА |
| YUV420 без сжатия или мезонинный файл |ДА |
| Apple ProRes 422 |ДА |
| Apple ProRes 422 LT |ДА |
| Apple ProRes 422 HQ |ДА |
| Apple ProRes Proxy |ДА |
| Apple ProRes 4444 |ДА |
| Apple ProRes 4444 XQ |ДА |
| HEVC/H.265| Профиль Main|

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Поддерживается |
| --- | --- |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |ДА |
| MPEG Layer 2 |ДА |
| MP3 (MPEG-1 Audio Layer 3) |ДА |
| Windows Media Audio |ДА |
| WAV/PCM |ДА |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |ДА |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |ДА |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |ДА |
| AMR (adaptive multi-rate) |ДА |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет |
| Dolby® E |Нет |
| Dolby® Digital (AC3) |Нет |
| Dolby® Digital Plus (E-AC3) |Нет |

## <a name="output-formats-and-codecs"></a>Выходные форматы и кодеки
В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.

| Формат файла | Видеокодек | Аудиокодек |
| --- | --- | --- |
| MP4 <br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (профили High, Main и Baselin) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Дальнейшие действия

[Создание преобразования с помощью настраиваемой предустановки](customize-encoder-presets-how-to.md)

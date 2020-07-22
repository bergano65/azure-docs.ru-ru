---
title: Схема выходных метаданных служб мультимедиа Azure | Документация Майкрософт
description: В этой статье приводятся общие сведения о схеме выходных метаданных служб мультимедиа Azure.
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
ms.date: 06/03/2020
ms.author: juliako
ms.openlocfilehash: 692fe12d12538bc35e3a22d4af1bd185839f69d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84418524"
---
# <a name="output-metadata"></a>Выходные метаданные

Задание кодирования связано с входным ресурсом-контейнером (или ресурсами-контейнерами), в котором нужно выполнить эти задачи. Например, преобразуйте MP4-файл в набор мультискоростных MP4-файлов в формате H.264. Создайте эскиз и наложения. После выполнения задачи создается выходной ресурс-контейнер.  Выходной ресурс содержит видео, аудио, эскизы и другие файлы. Выходной актив также содержит файл с метаданными выходного актива. Имя JSON-файла метаданных имеет следующий формат: `<source_file_name>_manifest.json` (например, `BigBuckBunny_manifest.json` ).  

Службы мультимедиа не проверяют входные ресурсы с вытеснением для создания метаданных. Входные метаданные создаются только в качестве артефакта при обработке входного ресурса в задании. Поэтому этот артефакт записывается в выходной ресурс. Для создания метаданных для входных ресурсов и выходных ресурсов используются различные средства. Таким образом, схемы входных и выходных метаданных немного отличаются.

В этой статье обсуждаются элементы и типы схемы JSON, на которых основаны выходные метаданные ( &lt; source_file_name &gt;_manifest.js). <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Полный код схемы и пример JSON можно найти в конце этой статьи.  

## <a name="assetfile"></a>AssetFile

Коллекция записей AssetFile для задания кодирования.  

| name | Описание: |
| --- | --- |
| **Исход** |Коллекция входных и исходных файлов мультимедиа, которая обрабатывалась для создания AssetFile.<br />Пример: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Каждый физический файл ресурса может содержать ноль или более видеодорожек, чередуемых в соответствующем формате ресурса-контейнера. <br />См. [VideoTracks](#videotracks). |
| **AudioTracks**|Каждый физический файл ресурса-контейнера может содержать ноль или более звуковых дорожек, чередуемых в соответствующем формате ресурса-контейнера. Это коллекция всех звуковых дорожек.<br /> Дополнительные сведения см. в разделе [AudioTracks](#audiotracks). |
| **Name**<br />Обязательное значение |Имя файла ресурса мультимедиа. <br /><br />Пример: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Размер**<br />Обязательное значение |Размер файла ресурса-контейнера в байтах. <br /><br />Пример: `"Size": 32414631`|
| **Длительность**<br />Обязательное значение |Длительность воспроизведения содержимого. Дополнительные сведения см. в разделе Формат [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) . <br /><br />Пример: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Каждый физический файл ресурса может содержать ноль или более видеодорожек, чередуемых в соответствующем формате ресурса-контейнера. Элемент **VideoTracks** — это коллекция всех видеодорожек.  

| name | Описание |
| --- | --- |
| **Id**<br /> Обязательное значение |Отсчитываемый от нуля индекс этой видеодорожки. **Примечание.**  Этот **идентификатор** необязательно является TrackID, используемым в MP4-файле. <br /><br />Пример: `"Id": 1`|
| **FourCC**<br />Обязательное значение | Код видеокодека FourCC, сообщаемый FFmpeg.  <br /><br />Пример: `"FourCC": "avc1"`|
| **Профиль** |Профиль H264 (применим только для кодека H264).  <br /><br />Пример: `"Profile": "High"` |
| **Уровень** |Уровень H264 (применим только для кодека H264).  <br /><br />Пример: `"Level": "3.2"`|
| **Width**<br />Обязательное значение |Ширина закодированного видео в пикселях.  <br /><br />Пример: `"Width": "1280"`|
| **Height**<br />Обязательное значение |Высота закодированного видео в пикселях.  <br /><br />Пример: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Обязательное значение|Числитель пропорции отображения видео.  <br /><br />Пример: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Обязательное значение |Знаменатель пропорции отображения видео.  <br /><br />Пример: `"DisplayAspectRatioDenominator": 9.0`|
| **Частота**<br />Обязательное значение |Измеренная частота видеокадров в формате 3F.  <br /><br />Пример: `"Framerate": 29.970`|
| **Bitrate**<br />Обязательное значение |Средняя скорость видео в битах за секунду, вычисленная из AssetFile. Подсчитываются только полезные данные элементарного потока, издержки на упаковку не включаются.  <br /><br />Пример: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Обязательное значение |Целевая средняя скорость для этой видеодорожки, запрошенная с помощью предустановки кодирования, в битах в секунду. <br /><br />Пример: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Каждый физический файл ресурса-контейнера может содержать ноль или более звуковых дорожек, чередуемых в соответствующем формате ресурса-контейнера. Элемент **AudioTracks** — это коллекция всех аудиодорожек.  

| name  | Описание |
| --- | --- |
| **Id**<br />Обязательное значение  |Отсчитываемый от нуля индекс этой звуковой дорожки. **Примечание.**  Это не обязательно TrackID, используемое в MP4-файле.  <br /><br />Пример: `"Id": 2`|
| **Равн**  |Строка кодека звуковой дорожки.  <br /><br />Пример: `"Codec": "aac"`|
| **Язык**|Пример: `"Language": "eng"`|
| **Channels**<br />Обязательное значение|Число аудиоканалов.  <br /><br />Пример: `"Channels": 2`|
| **SamplingRate**<br />Обязательное значение |Частота аудиовыборки: выборок/с или Гц.  <br /><br />Пример: `"SamplingRate": 48000`|
| **Bitrate**<br />Обязательное значение |Средняя скорость аудиопотока в битах в секунду, вычисленная на основе файла ресурса-контейнера. Подсчитываются только полезные данные элементарного потока, издержки на упаковку не включаются.  <br /><br />Пример: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Пример схемы JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие шаги

[Создание входных данных задания из URL-адреса HTTPS](job-input-from-http-how-to.md)

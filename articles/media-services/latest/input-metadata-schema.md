---
title: Схема входных метаданных служб мультимедиа Azure v3
description: В этой статье приводятся общие сведения о схеме входных метаданных служб мультимедиа Azure версии 3.
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
ms.date: 04/14/2020
ms.author: juliako
ms.openlocfilehash: 40e61061878c8aec6bad353bfd0c5f2f4178ce14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85095571"
---
# <a name="input-metadata"></a>Входные метаданные 

Задание кодирования связано с входным ресурсом-контейнером (или ресурсами-контейнерами), в котором нужно выполнить эти задачи.  После выполнения задачи создается выходной ресурс-контейнер. Выходной ресурс содержит видео, аудио, эскизы, манифест и другие файлы. 

Выходной актив также содержит файл с метаданными входного актива. Имя JSON-файла метаданных имеет случайный идентификатор, не используйте его для идентификации входного ресурса, к которому принадлежит выходной ресурс. Чтобы определить входной ресурс, к которому он принадлежит, используйте `Uri` поле (Дополнительные сведения см. в разделе [другие дочерние элементы](#other-child-elements)).  

Службы мультимедиа не проверяют входные ресурсы с вытеснением для создания метаданных. Входные метаданные создаются только в качестве артефакта при обработке входного ресурса в задании. Поэтому этот артефакт записывается в выходной ресурс. Для создания метаданных для входных ресурсов и выходных ресурсов используются различные средства. Таким образом, схемы входных и выходных метаданных немного отличаются.

В этой статье обсуждаются элементы и типы схемы JSON, на которых основан входной метаданных ( &lt; asset_id &gt;_metadata.json). Дополнительные сведения о файле, содержащем метаданные о выходном ресурсе, см. в разделе [выходные метаданные](output-metadata-schema.md).  

Пример схемы JSON можно найти в конце этой статьи.  

## <a name="assetfile"></a>AssetFile  

Содержит коллекцию элементов AssetFile для задания кодирования.  

> [!NOTE]
> Следующие четыре дочерних элемента должны появляться последовательно.  
> 
> 

| name  | Описание: |
| --- | --- | 
| **VideoTracks**|Каждый физический файл ресурса может содержать ноль или более видеодорожек, чередуемых в соответствующем формате ресурса. Дополнительные сведения см. в разделе [VideoTracks](#videotracks). |
| **AudioTracks**|Каждый физический файл ресурса-контейнера может содержать ноль или более звуковых дорожек, чередуемых в соответствующем формате ресурса-контейнера. Дополнительные сведения см. в разделе [AudioTracks](#audiotracks) |
| **Метаданные**  |Метаданные файла ресурса-контейнера, представленные в виде строки "ключ —значение". <br />Например: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Другие дочерние элементы

| name | Описание |
| --- | --- |
| **Name**<br />Обязательное значение |Имя файла ресурса-контейнера. <br /><br />Пример: `"Name": "Ignite-short.mp4"` |
| **URI**<br />Обязательное значение |URL-адрес для расположения входного ресурса. Для идентификации входного ресурса, к которому принадлежит выходной ресурс, используйте `Uri` поле вместо идентификатора.|
| **Размер**<br />Обязательное значение |Размер файла ресурса-контейнера в байтах.  <br /><br />Пример: `"Size": 75739259`|
| **Длительность**<br />Обязательное значение |Длительность воспроизведения содержимого. <br /><br />Например, `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Обязательное значение |Количество потоков в файле ресурса-контейнера.  <br /><br />Пример: `"NumberOfStreams": 2`|
| **FormatNames**<br />Обязательное значение |Имена форматов.  <br /><br />Пример: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **форматвербосенаме**<br /> Обязательное значение |Подробные имена форматов. <br /><br />Пример: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Время начала содержимого.  <br /><br />Пример: `"StartTime": "PT0S"` |
| **OverallBitRate** |Средняя скорость файла ресурса в битах в секунду.  <br /><br />Пример: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| name |  | Описание: |
| --- | --- |
| **FourCC**<br />Обязательное значение |Код видеокодека FourCC, сообщаемый FFmpeg.<br /><br />Пример: `"FourCC": "avc1"` |
| **Профиль** |Профиль видеодорожки. <br /><br />Пример: `"Profile": "Main"`|
| **Уровень** |Уровень видеодорожки. <br /><br />Пример: `"Level": "3.2"`|
| **PixelFormat** |Формат пикселей видеодорожки. <br /><br />Пример: `"PixelFormat": "yuv420p"`|
| **Width**<br />Обязательное значение |Ширина закодированного видео в пикселях. <br /><br />Пример: `"Width": "1280"`|
| **Height**<br />Обязательное значение |Высота закодированного видео в пикселях.<br /><br />Пример: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Обязательное значение |Числитель пропорции отображения видео.<br /><br />Пример: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Обязательное значение |Знаменатель пропорции отображения видео. <br /><br />Пример: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Числитель пропорции выборки видео. <br /><br />Пример: `"SampleAspectRatioNumerator": 1.0`|
| **самплеаспектратиоденоминатор**|Пример: `"SampleAspectRatioDenominator": 1.0`|
| **Частота**<br />Обязательное значение |Измеренная частота видеокадров в формате 3F. <br /><br />Пример: `"FrameRate": 29.970`|
| **Bitrate** |Средняя скорость видео в битах в секунду, вычисленная на основе файла ресурса. Подсчитываются только полезные данные элементарного потока, издержки на упаковку не включаются. <br /><br />Пример: `"Bitrate": 8421583`|
| **HasBFrames** |Количество видеодорожек с кадрами B. <br /><br />Пример: `"HasBFrames": 2`|
| **Метаданные** |Общие строки "ключ —значение", которые можно использовать для хранения различных сведений. <br />Полный пример см. в конце статьи. |
| **Id**<br />Обязательное значение |Отсчитываемый от нуля индекс аудио- или видеодорожки.<br /><br /> Этот **идентификатор** не обязательно представляет собой TrackID, используемый в файле MP4. <br /><br />Пример: `"Id": 2`|
| **Равн** |Строка кодека видеодорожки. <br /><br />Пример: `"Codec": "h264"`|
| **CodecLongName** |Полное имя кодека звуковой или видеодорожки. <br /><br />Пример: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Равн** |Строка кодека видеодорожки. <br /><br />Пример: `"Codec": "h264"`|
| **TimeBase**<br />Обязательное значение |Базовый счетчик времени.<br /><br />Пример: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Количество кадров (для видеодорожек). <br /><br />Пример: `"NumberOfFrames": 2107`|
| **StartTime** |Время начала дорожки.<br /><br />Пример: `"StartTime": "PT0.033S"` |
| **Длительность** |Продолжительность дорожки. <br /><br />Пример: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| name  | Описание: |
| --- | --- | 
| **SampleFormat** |Формат выборки. <br /><br />Пример: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Структура канала. <br /><br />Пример: `"ChannelLayout": "stereo"`|
| **Channels**<br />Обязательное значение |Количество (0 или более) аудиоканалов. <br /><br />Пример: `"Channels": 2`|
| **SamplingRate**<br />Обязательное значение |Частота аудиовыборки: выборок/с или Гц. <br /><br />Пример: `"SamplingRate": 48000`|
| **Bitrate** |Средняя скорость аудиопотока в битах в секунду, вычисленная на основе файла ресурса-контейнера. Подсчитываются только полезные данные элементарного потока, издержки на упаковку не включаются. <br /><br />Пример: `"Bitrate": 192080`|
| **Метаданные** |Общие строки "ключ —значение", которые можно использовать для хранения различных сведений.  <br />Полный пример см. в конце статьи. |
| **Id**<br />Обязательное значение |Отсчитываемый от нуля индекс аудио- или видеодорожки.<br /><br /> MP4-файл не обязательно должен содержать идентификатор дорожки. <br /><br />Пример: `"Id": 1`|
| **Равн** |Строка кодека видеодорожки. <br /><br />Пример: `"Codec": "aac"`|
| **CodecLongName** |Полное имя кодека звуковой или видеодорожки. <br /><br />Пример: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />Обязательное значение |Базовый счетчик времени.<br /><br />Пример: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Количество кадров (для видеодорожек). <br /><br />Пример: `"NumberOfFrames": 3294`|
| **StartTime** |Время начала дорожки. Дополнительные сведения см. в разделе [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Пример: `"StartTime": "PT0S"` |
| **Длительность** |Продолжительность дорожки. <br /><br />Пример: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Метаданные

| Имя | Описание |
| --- | --- |
| **key**<br />Обязательное значение |Ключ в паре "ключ — значение". |
| **value**<br /> Обязательное значение |Значение в паре "ключ — значение". |

## <a name="schema-example"></a>Пример схемы

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие шаги

[Выходные метаданные](output-metadata-schema.md)

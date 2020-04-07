---
title: Получить информацию о преобразованной модели
description: Описание всех параметров преобразования модели
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681523"
---
# <a name="get-information-about-a-converted-model"></a>Получить информацию о преобразованной модели

Файл arrAsset, созданный службой преобразования, предназначен исключительно для потребления службой рендеринга. Однако иногда, когда требуется получить доступ к информации о модели, без запуска сеанса рендеринга, могут быть случаи. Таким образом, служба конверсии помещает файл JSON рядом с файлом arrAsset в выходном контейнере. Например, если `buggy.gltf` файл преобразуется, выходной контейнер `buggy.info.json` будет содержать `buggy.arrAsset`файл, называемый рядом с преобразованным активом. Он содержит информацию об исходной модели, преобразованной модели и о самом преобразовании.

## <a name="example-info-file"></a>Пример *файла информации*

Вот пример *файла информации,* подготовленного `buggy.gltf`путем преобразования файла под названием:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Информация в файле информации

### <a name="the-files-section"></a>Раздел *файлов*

Этот раздел содержит предоставленные имена файлов.

* `input`: Имя исходного файла.
* `output`: Имя выходного файла, когда пользователь указал имя без дефолта.

### <a name="the-conversionsettings-section"></a>Раздел *конверсииНастройки*

В этом разделе содержится копия [ConversionSettings,](configure-model-conversion.md#settings-file) указанная при преобразовании модели.

### <a name="the-inputinfo-section"></a>Раздел *ВхайтФофо*

В этом разделе записывается информация о формате исходного файла.

* `sourceAssetExtension`: Расширение файла исходного файла.
* `sourceAssetFormat`: Описание формата исходного файла.
* `sourceAssetFormatVersion`: Версия формата исходного файла.
* `sourceAssetGenerator`: Имя инструмента, который генерировал исходный файл, если он доступен.

### <a name="the-inputstatistics-section"></a>Раздел *Вхтотстатистика*

В этом разделе содержится информация об исходной сцене. Часто возникают расхождения между значениями в этом разделе и эквивалентными значениями в инструменте, создавом исходную модель. Такие различия ожидаются, так как модель модифицируется во время этапов экспорта и преобразования.

* `numMeshes`: Количество деталей сетки, где каждая часть может ссылаться на один материал.
* `numFaces`: Общее количество _треугольников_ во всей модели. Обратите внимание, что сетка триангуляции во время преобразования.
* `numVertices`: Общее количество головорающих в целом модели.
* `numMaterial`: Общее количество материалов в целом модели.
* `numFacesSmallestMesh`: Количество треугольников в самой маленькой сетке модели.
* `numFacesBiggestMesh`: Количество треугольников в самой большой сетке модели.
* `numNodes`: Количество узлов на графике сцены модели.
* `numMeshUsagesInScene`: Количество раз узлы ссылаются сетки. Более одного узла может ссылаться на ту же сетку.
* `maxNodeDepth`: Максимальная глубина узлов в графике сцены.

### <a name="the-outputinfo-section"></a>Раздел *outputInfo*

В этом разделе записывается общая информация о генерируемом выходе.

* `conversionToolVersion`: Версия преобразователя модели.
* `conversionHash`: Хэш данных в arrAsset, которые могут способствовать рендерингу. Можно использовать для понимания того, произвела ли служба конверсии другой результат при повторном запуске в одном файле.

### <a name="the-outputstatistics-section"></a>Раздел *выходнаястатистика*

В этом разделе записывается информация, рассчитанная из конвертированных активов.

* `numMeshPartsCreated`: Количество сеток в arrAsset. Он может `numMeshes` отличаться `inputStatistics` от в разделе, потому что инстансирование зависит от процесса преобразования.
* `numMeshPartsInstanced`: Количество сеток, которые повторно используются в arrAsset.
* `recenteringOffset`: Когда `recenterToOrigin` опция в [ConversionSettings](configure-model-conversion.md) включена, это значение является переводом, который переместит преобразованную модель обратно в исходное положение.
* `boundingBox`: Границы модели.

## <a name="next-steps"></a>Дальнейшие действия

* [Преобразование модели](model-conversion.md)
* [Настройка преобразования модели](configure-model-conversion.md)

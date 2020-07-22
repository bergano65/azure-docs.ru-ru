---
title: Получение сведений о преобразованной модели
description: Описание всех параметров преобразования модели.
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 722d3e218272202074820db442ab1592042c7011
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805005"
---
# <a name="get-information-about-a-converted-model"></a>Получение сведений о преобразованной модели

Файл Аррассет, созданный службой преобразования, предназначен исключительно для использования службой визуализации. Однако иногда требуется получить доступ к сведениям о модели без запуска сеанса отрисовки. Таким образом, служба преобразования помещает JSON-файл рядом с файлом Аррассет в контейнер выходных данных. Например, при `buggy.gltf` преобразовании файла выходной контейнер будет содержать файл, вызываемый `buggy.info.json` рядом с преобразованным ресурсом `buggy.arrAsset` . Он содержит сведения об исходной модели, преобразованной модели и о самом преобразовании.

## <a name="example-info-file"></a>Пример файла *сведений*

Ниже приведен пример файла *сведений* , полученного при преобразовании файла с именем `buggy.gltf` :

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

## <a name="information-in-the-info-file"></a>Сведения в файле сведений

### <a name="the-files-section"></a>Раздел " *файлы* "

Этот раздел содержит указанные имена файлов.

* `input`: Имя исходного файла.
* `output`: Имя выходного файла, если пользователь указал имя, отличное от имени по умолчанию.

### <a name="the-conversionsettings-section"></a>Раздел *конверсионсеттингс*

В этом разделе содержится копия [конверсионсеттингс](configure-model-conversion.md#settings-file) , заданная при преобразовании модели.

### <a name="the-inputinfo-section"></a>Раздел *инпутинфо*

В этом разделе записываются сведения о формате исходного файла.

* `sourceAssetExtension`: Расширение файла исходного кода.
* `sourceAssetFormat`: Описание формата исходного файла.
* `sourceAssetFormatVersion`: Версия формата исходного файла.
* `sourceAssetGenerator`: Имя инструмента, создавшего исходный файл, если он доступен.

### <a name="the-inputstatistics-section"></a>Раздел *инпутстатистикс*

В этом разделе содержатся сведения об исходной сцене. Часто возникают различия между значениями в этом разделе и эквивалентными значениями в средстве, которое создало исходную модель. Такие различия ожидаемы, поскольку модель изменяется во время экспорта и преобразования.

* `numMeshes`: Количество частей сетки, где каждая часть может ссылаться на один материал.
* `numFaces`: Общее число _треугольников_ во всей модели. Обратите внимание, что при преобразовании сетка будет триангуляцией. Это значение влияет на предел многоугольника в [стандартном размере виртуальной машины отрисовки](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: Общее количество вершин во всей модели.
* `numMaterial`: Общее количество материалов во всей модели.
* `numFacesSmallestMesh`: Число треугольников в наименьшей сетке модели.
* `numFacesBiggestMesh`: Число треугольников в самой крупной сетке модели.
* `numNodes`: Количество узлов в графе сцены модели.
* `numMeshUsagesInScene`: Количество ссылок на узлы в сети. Несколько узлов могут ссылаться на одну и ту же сеть.
* `maxNodeDepth`: Максимальная глубина узлов в графе сцены.

### <a name="the-outputinfo-section"></a>Раздел *аутпутинфо*

В этом разделе записываются общие сведения о созданных выходных данных.

* `conversionToolVersion`: Версия преобразователя модели.
* `conversionHash`: Хэш данных в Аррассет, которые могут участвовать в подготовке к просмотру. Можно использовать, чтобы определить, создал ли служба преобразования другой результат при повторном запуске в том же файле.

### <a name="the-outputstatistics-section"></a>Раздел *аутпутстатистикс*

В этом разделе записываются сведения, вычисленные на основе преобразованного ресурса.

* `numMeshPartsCreated`: Количество сеток в Аррассет. Он может отличаться от `numMeshes` в `inputStatistics` разделе, так как процесс преобразования влияет на создание экземпляров.
* `numMeshPartsInstanced`: Количество сеток, которые повторно используются в Аррассет.
* `recenteringOffset`: Если `recenterToOrigin` параметр в [конверсионсеттингс](configure-model-conversion.md) включен, это значение является переводом, который переместит преобразованную модель обратно в ее исходную точку.
* `boundingBox`: Границы модели.

## <a name="next-steps"></a>Дальнейшие действия

* [Преобразование модели](model-conversion.md)
* [Настройка преобразования модели](configure-model-conversion.md)

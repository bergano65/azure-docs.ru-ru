---
title: Переопределение материалов во время преобразования модели
description: Описывает рабочий процесс переопределения материала во время преобразования
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681484"
---
# <a name="override-materials-during-model-conversion"></a>Переопределение материалов во время преобразования модели

Во время преобразования параметры материала в исходной модели используются для определения [материалов PBR](../../overview/features/pbr-materials.md) , используемых модулем подготовки отчетов.
Иногда [Преобразование по умолчанию](../../reference/material-mapping.md) не дает желаемых результатов и вам необходимо внести изменения.
При преобразовании модели для использования в удаленной отрисовке Azure можно предоставить файл переопределения материалов, чтобы настроить способ преобразования материалов на основе каждого материала.
Раздел о [настройке преобразования модели](configure-model-conversion.md) содержит инструкции по объявлению имени файла переопределения материала.

## <a name="the-override-file-used-during-conversion"></a>Файл переопределения, используемый во время преобразования

В качестве простого примера предположим, что модель Box содержит один материал, именуемый «Default». Необходимо настроить цвет албедо для использования в ARR.
В этом случае `box_materials_override.json` файл можно создать следующим образом:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box_materials_override.json`Файл помещается во входной контейнер, а `ConversionSettings.json` добавляется рядом с параметром `box.fbx` , который указывает преобразованию, где найти файл переопределения (см. раздел [Настройка преобразования модели](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

При преобразовании модели будут применены новые параметры.

### <a name="color-materials"></a>Цветовые материалы

Модель [цветового материала](../../overview/features/color-materials.md) описывает постоянно затененную поверхность, которая не зависит от освещения.
Это полезно для ресурсов, выполняемых алгоритмами Фотограмметри, например.
В файлах переопределения материалов можно объявить материал как цветный материал, задав `unlit` для значение `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Игнорировать определенные карты текстур

Иногда может потребоваться, чтобы процесс преобразования игнорировал определенные карты текстуры. Это может быть вызвано тем, что модель создала средство, которое создает специальные карты, неправильно распознаваемые модулем подготовки отчетов. Например, "ОпаЦитимап", который используется для определения чего-либо, кроме непрозрачности, или модели, где "Нормалмап" хранится как "Бумпмап". (В последнем случае необходимо игнорировать "Нормалмап", что приведет к тому, что преобразователь будет использовать "Бумпмап" как "Нормалмап".)

Принцип прост. Просто добавьте свойство с именем `ignoreTextureMaps` и добавьте любую карту текстуры, которую нужно игнорировать:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Полный список текстурных карт, которые можно игнорировать, см. в схеме JSON ниже.

## <a name="json-schema"></a>Схема JSON

Полная схема JSON для файлов материалов указана здесь. За исключением `unlit` и `ignoreTextureMaps` , доступные свойства являются подмножеством свойств, описанных в разделах модели [цветового материала](../../overview/features/color-materials.md) и [PBR Material](../../overview/features/pbr-materials.md) .

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Цветовые материалы](../../overview/features/color-materials.md)
* [Материалы PBR](../../overview/features/pbr-materials.md)
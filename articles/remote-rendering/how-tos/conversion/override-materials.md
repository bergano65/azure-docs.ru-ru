---
title: Переопределение материалов при преобразовании модели
description: Объясняет переопределение материала на момент преобразования
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681484"
---
# <a name="override-materials-during-model-conversion"></a>Переопределение материалов при преобразовании модели

Во время преобразования параметры материала в исходной модели используются для определения [материалов PBR, используемых](../../overview/features/pbr-materials.md) рендерером.
Иногда [конверсия по умолчанию](../../reference/material-mapping.md) не дает желаемых результатов, и вам нужно внести изменения.
При преобразовании модели для использования в удаленном рендерезе Azure можно предоставить файл переопределения материалов, чтобы настроить способ преобразования материала на основе в ней.
В разделе, [предускряжающих преобразование модели,](configure-model-conversion.md) есть инструкции по объявлению значения файла.

## <a name="the-override-file-used-during-conversion"></a>Переопределение файла, используемого во время преобразования

В качестве простого примера можно сказать, что модель коробки имеет единый материал, называемый "По умолчанию". Цвет альбедо должен быть скорректирован для использования в ARR.
В этом случае `box_materials_override.json` файл может быть создан следующим образом:

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

Файл `box_materials_override.json` помещается в контейнер ввода, `ConversionSettings.json` и `box.fbx`добавляется рядом, который говорит конверсии, где найти переопределение файла (см. [Настройка преобразования модели):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

При преобразовании модели будут применяться новые настройки.

### <a name="color-materials"></a>Цветные материалы

Цветовая модель [описывает](../../overview/features/color-materials.md) постоянно затененные поверхности, которая не зависит от освещения.
Это полезно для активов, сделанных, например, с помощью алгоритмов фотограмметрии.
В файлах переопределения материала материал может быть объявлен `unlit` цветным материалом, установив на `true`.

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

### <a name="ignore-specific-texture-maps"></a>Игнорировать конкретные текстурные карты

Иногда может потребоваться, чтобы процесс преобразования игнорировал определенные текстурные карты. Это может быть в случае, когда модель была создана инструментом, который генерирует специальные карты, неправильно понятые рендерером. Например, "OpacityMap", который используется для определения чего-то иного, кроме непрозрачности, или модель, в которой "NormalMap" хранится как "BumpMap". (В последнем случае вы хотите проигнорировать "NormalMap", что приведет к тому, что конвертер будет использовать "BumpMap" в качестве "NormalMap".)

Принцип прост. Просто добавьте `ignoreTextureMaps` свойство вызова и добавьте любую текстуру карты, которые вы хотите игнорировать:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Полный список текстурных карт, которые можно игнорировать, смотрите схему JSON ниже.

## <a name="json-schema"></a>Схема JSON

Полная схема JSON для файлов материалов приведена здесь. За исключением `unlit` `ignoreTextureMaps`и , свойства доступны подмножество свойств, описанных в разделах на [цветовой материал](../../overview/features/color-materials.md) и [PBR материальной](../../overview/features/pbr-materials.md) модели.

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

## <a name="next-steps"></a>Дальнейшие действия

* [Цветные материалы](../../overview/features/color-materials.md)
* [Материалы PBR](../../overview/features/pbr-materials.md)
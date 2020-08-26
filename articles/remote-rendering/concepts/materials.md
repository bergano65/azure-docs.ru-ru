---
title: Материалы
description: Описания материалов для отрисовки и свойств материалов
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 7e8b670a084e80fcd7f48dcb6e97a943c90bdf0e
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893040"
---
# <a name="materials"></a>Материалы

Материалы представляют собой [совместно используемые ресурсы](../concepts/lifetime.md), которые определяют параметры отрисовки для [сеток](meshes.md). Материалы позволяют указать, какие [текстуры](textures.md) нужно применять, нужно ли делать объекты прозрачными, как следует вычислять освещение.

Материалы автоматически создаются во время [преобразования модели](../how-tos/conversion/model-conversion.md) и становятся доступны во время выполнения. Вы также можете создавать в коде собственные материалы и заменять ими существующие. Такой сценарий особенно подходит, если вы хотите использовать один материал для многих сеток. Так как изменения материала будут отражаться во всех сетках, которые ссылаются на него, такой способ позволяет легко вносить изменения.

> [!NOTE]
> Некоторые сценарии использования, например выделение взятого объекта, можно выполнять через изменение материалов, но гораздо эффективнее обрабатывать их в [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Типы материалов

В службе "Удаленная отрисовка Azure" используется два разных типа материалов.

* [Материалы PBR](../overview/features/pbr-materials.md) предназначены для поверхностей, которые должны быть как можно более точными с физической точки зрения. Для этих материалов вычисляется реалистичное освещение с помощью *физически корректной отрисовки* (PBR). Для достижения максимальной пользы от материалов этого типа важно предоставить высококачественные входные данные, в том числе карты неровностей и нормалей.

* [Цветовые материалы](../overview/features/color-materials.md) используются в тех случаях, когда дополнительное освещение не предполагается. Эти материалы всегда считаются полностью освещенными, что упрощает настройку. Цветовые материалы используются в тех случаях, когда освещение не учитывается совсем или вычисляется для статической сцены, например в моделях, полученных методом [фотограмметрии](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Сетка или MeshComponent при назначении материалов

[Сетки](meshes.md) имеют одну или несколько подсеток. Каждая подсетка ссылается на один материал. Вы можете изменить используемый материал непосредственно для сетки или переопределить материал для подсетки, изменяя узел [MeshComponent](meshes.md#meshcomponent).

Если вы изменяете материал непосредственно для ресурса сетки, это изменение затрагивает все экземпляры этой сетки. Но изменение в MeshComponent влияет только на один конкретный экземпляр сетки. Выбор оптимального метода зависит от того, какое поведение более предпочтительно. Более распространен вариант изменения MeshComponent.

## <a name="material-de-duplication"></a>Дедупликация материалов

Во время преобразования несколько материалов с одинаковыми свойствами и текстурами автоматически дедуплицируются в один материал. Это поведение можно отключить в [параметрах преобразования](../how-tos/conversion/configure-model-conversion.md), но мы рекомендуем сохранять его для повышения производительности.

## <a name="material-classes"></a>Классы материалов

Все материалы, предоставляемые в API, являются производными от базового класса `Material`. Их тип можно запрашивать через `Material.MaterialSubType` или путем прямого приведения:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>Дальнейшие действия

* [Материалы PBR](../overview/features/pbr-materials.md)
* [Цветовые материалы](../overview/features/color-materials.md)

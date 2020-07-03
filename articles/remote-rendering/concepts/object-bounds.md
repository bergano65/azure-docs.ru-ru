---
title: Границы объектов
description: Объясняется, как можно запрашивать границы пространственных объектов
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681718"
---
# <a name="object-bounds"></a>Границы объектов

Границы объектов представляют том, занимаемый [сущностью](entities.md) и ее дочерними элементами. В удаленной отрисовке Azure границы объектов всегда задаются в виде *ограничивающих прямоугольников с согласованием по осям* (ААББ). Границы объектов могут быть либо в *локальном пространстве* , либо в *мировом пространстве*. В любом случае они всегда ориентированы на оси. Это означает, что экстенты и объемы могут отличаться между локальным и мировым представлением пространства.

## <a name="querying-object-bounds"></a>Запросы к привязкам объектов

Локальный ААББ [сети](meshes.md) можно запрашивать непосредственно из ресурса сетки. Эти границы могут быть преобразованы в локальное или мировое пространство сущности с помощью преобразования сущности.

Можно вычислить границы всей иерархии объектов таким образом, но для этого необходимо выполнить обход иерархии, запросить границы для каждой сетки и объединить их вручную. Эта операция является утомительной и неэффективной.

Лучшим способом является вызов `QueryLocalBoundsAsync` или `QueryWorldBoundsAsync` для сущности. Затем вычисления разгружаются на сервер и возвращаются с минимальной задержкой.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Пространственные запросы](../overview/features/spatial-queries.md)

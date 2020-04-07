---
title: Границы объектов
description: Объясняет, как можно запрашивать границы пространственных объектов
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681718"
---
# <a name="object-bounds"></a>Границы объектов

Границы объектов представляют объем, который занимает [сущность](entities.md) и ее дети. В удаленном рендеринге Azure всегда приводятся границы объектов в виде *выровненных оси ограничивающих коробок* (AABB). Границы объектов могут находиться как в *локальном пространстве,* так и в *мировом пространстве.* В любом случае, они всегда выровнены по оси, что означает, что масштабы и объем могут отличаться между местным и мировым космическим представительством.

## <a name="querying-object-bounds"></a>Границы объектов запроса

Локальный AABB [сетки](meshes.md) может быть запрошен непосредственно с ресурса сетки. Эти границы могут быть преобразованы в локальное пространство или мировое пространство сущности с помощью преобразования сущности.

Таким образом можно вычислить границы всей иерархии объектов, но для этого необходимо пройти иерархию, запросить границы для каждой сетки и объединить их вручную. Эта операция является утомительной и неэффективной.

Лучший способ — `QueryLocalBoundsAsync` `QueryWorldBoundsAsync` позвонить или на объекте. Вычисление затем разгружается на сервер и возвращается с минимальной задержкой.

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

## <a name="next-steps"></a>Дальнейшие действия

* [Пространственные запросы](../overview/features/spatial-queries.md)

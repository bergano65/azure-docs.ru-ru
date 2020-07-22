---
title: Границы объектов
description: Объяснение того, как можно запрашивать границы пространственных объектов
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d9f970d08318d7dec685d3021c72b7f80de90049
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758883"
---
# <a name="object-bounds"></a>Границы объектов

Границы объектов представляют том, занятый [сущностью](entities.md) и ее дочерними элементами. В удаленной отрисовке Azure границы объектов всегда задаются в виде *выровненных по оси ограничивающих прямоугольников* (AABB). Границы объектов могут находиться либо в *локальном пространстве*, либо в *мировом пространстве*. В любом случае они всегда выравниваются по оси. Это означает, что экстенты и объемы могут отличаться между локальным и мировым представлением пространства.

## <a name="querying-object-bounds"></a>Запросы границ объектов

Локальный AABB [сетки](meshes.md) можно запрашивать непосредственно из ресурса сетки. Эти границы можно преобразовать в локальное или мировое пространство сущности с помощью преобразования сущности.

Таким образом можно вычислить границы всей иерархии объектов, но для этого необходимо выполнить обход иерархии, запросить границы для каждой сетки и объединить их вручную. Эта операция утомительна и неэффективна.

Лучше будет вызвать `QueryLocalBoundsAsync` или `QueryWorldBoundsAsync` на сущности. Тогда вычисления передаются на сервер и возвращаются с минимальной задержкой.

```cs
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

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->IsRanToCompletion())
        {
            Double3 aabbMin = bounds->Result()->min;
            Double3 aabbMax = bounds->Result()->max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Пространственные запросы](../overview/features/spatial-queries.md)

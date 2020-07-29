---
title: Вырезание плоскостей
description: Описывается, что представляют из себя сечения и как их использовать.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 7adf9a9701eb2492f0b13a26af1dbaf8de631373
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021370"
---
# <a name="cut-planes"></a>Вырезание плоскостей

*Сечение* — это визуальная функция, которая обрезает пиксели с одной стороны виртуальной плоскости, показывая срез [сетки](../../concepts/meshes.md).
Эффект показан на рисунке ниже. Слева показана исходная сетка, а справа можно увидеть, что находится внутри.

![Сечение](./media/cutplane-1.png)

## <a name="limitations"></a>Ограничения

* В настоящее время Удаленная отрисовка Azure поддерживает **не более восьми активных сечений**. Создать можно и больше, но если вы попытаетесь включить одновременно больше восьми, этого не произойдет. Если нужно поменять компонент, влияющий на сцену, сначала отключите другие плоскости.
* Каждое сечение влияет на все удаленно отрисовываемые объекты. В настоящее время невозможно исключить определенные объекты или части сетки.
* Сечения — это исключительно визуальная функция, которая не влияет на результаты [пространственных запросов](spatial-queries.md). Если вы хотите выполнить пересечение лучом применительно к сетке с сечением, можно настроить начальную точку луча так, чтобы она находилась на сечении. В результате луч сможет падать только на видимые части.

## <a name="performance-considerations"></a>Вопросы производительности

На отрисовку каждого активного сечения требуются некоторые затраты ресурсов. Отключайте или удаляйте сечения, если они больше не нужны.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Чтобы добавить сечение в сцену, создайте объект *CutPlaneComponent*. Расположение и ориентация плоскости определяются [сущностью](../../concepts/entities.md) владельца компонента.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Свойства CutPlaneComponent

Компонент сечения имеет перечисленные ниже свойства.

* `Enabled`: Сечения можно временно отключать, отключая компонент. Отключенные сечения не требуют затрат на отрисовку, а также не включаются в общее количество сечений.

* `Normal`: Указывает, какое направление (+X,-X,+Y,-Y,+Z,-Z) используется в качестве нормали к плоскости. Это направление определяется относительно ориентации сущности владельца. Для точного размещения переместите и поверните сущность владельца.

* `FadeColor` и `FadeLength`:

  Если альфа-значение *FadeColor* не равно нулю, пиксели, находящиеся близко к сечению, будут выцветать в направлении компонента RGB FadeColor. Интенсивность альфа-канала определяет то, будут ли пиксели выцветать до выбранного цвета полностью или частично. *FadeLength* определяет расстояние, на котором будет происходить выцветание.

## <a name="next-steps"></a>Дальнейшие действия

* [Односторонняя отрисовка](single-sided-rendering.md)
* [Пространственные запросы](spatial-queries.md)

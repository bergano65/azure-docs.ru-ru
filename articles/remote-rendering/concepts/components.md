---
title: Компоненты
description: Определение компонентов в области удаленной подготовки к просмотру Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681900"
---
# <a name="components"></a>Компоненты

Удаленная визуализация Azure использует [системный шаблон компонента сущности](https://en.wikipedia.org/wiki/Entity_component_system) . Хотя [сущности](entities.md) представляют собой расположение и иерархическую композицию объектов, компоненты отвечают за реализацию поведения.

Наиболее часто используемые типы компонентов — это [компоненты сетки](meshes.md), которые добавляют сетки в конвейер отрисовки. Аналогичным образом, [компоненты Light](../overview/features/lights.md) используются для добавления освещения и [вырезание компонентов плоскости](../overview/features/cut-planes.md) для вырезания открытых сеток.

Все эти компоненты используют преобразование (расположение, поворот, масштабирование) сущности, к которой они присоединены, в качестве контрольной точки.

## <a name="working-with-components"></a>Работа с компонентами

Можно легко добавлять, удалять и манипулировать компонентами программным способом.

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Компонент прикрепляется к сущности во время создания. После этого его нельзя переместить в другую сущность. Компоненты явным образом удаляются `Component.Destroy()` с или автоматически при уничтожении сущности-владельца компонента.

Только один экземпляр каждого типа компонента может быть добавлен в сущность за раз.

## <a name="unity-specific"></a>Зависит от Unity

Интеграция Unity имеет дополнительные функции расширения для взаимодействия с компонентами. См. раздел [объекты и компоненты игры Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Дальнейшие шаги

* [Границы объектов](object-bounds.md)
* [Сетки](meshes.md)

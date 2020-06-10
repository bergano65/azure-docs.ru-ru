---
title: Components
description: Определение компонентов в области Удаленной отрисовки Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 42efc1395fee28a3854420f15c2ad08b301cc1f7
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758747"
---
# <a name="components"></a>Components

Удаленная отрисовка Azure использует шаблон проектирования [Entity-Component-System](https://en.wikipedia.org/wiki/Entity_component_system) (Система компонентов cущностей). [Сущности](entities.md) представляют собой данные о расположении и иерархической композиции объектов, а компоненты отвечают за реализацию поведения.

Наиболее часто используемые типы компонентов — это [компоненты сетки](meshes.md), которые добавляют сетки в конвейер отрисовки. Аналогичным образом [компоненты освещения](../overview/features/lights.md) используются для добавления освещения, а [компоненты сечения](../overview/features/cut-planes.md) — для сечения открытых сеток.

Все эти компоненты используют преобразование (изменение расположения, поворота, масштабирование) сущности, к которой они присоединены, в качестве опорной точки.

## <a name="working-with-components"></a>Работа с компонентами

Вы можете легко программно добавлять и удалять компоненты и оперировать ими.

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

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Компонент прикрепляется к сущности во время создания. После этого его нельзя передать другой сущности. Компоненты явным образом удаляются с помощью функции `Component.Destroy()` или это происходит автоматически при уничтожении сущности-владельца компонента.

За раз к сущности можно добавить только один экземпляр каждого типа компонента.

## <a name="unity-specific"></a>Особенности при работе с Unity

Интеграция Unity предоставляет дополнительные функции расширения для взаимодействия с компонентами. Дополнительную информацию см. в статье [Игровые объекты и компоненты Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Границы объектов](object-bounds.md)
* [Сетки](meshes.md)

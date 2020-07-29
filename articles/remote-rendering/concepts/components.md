---
title: Компоненты
description: Определение компонентов в области Удаленной отрисовки Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: bd2a6e16d34bf6c5b59ce7fa5c99975d44947770
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021676"
---
# <a name="components"></a>Components

Удаленная отрисовка Azure использует шаблон проектирования [Entity-Component-System](https://en.wikipedia.org/wiki/Entity_component_system) (Система компонентов cущностей). [Сущности](entities.md) представляют собой данные о расположении и иерархической композиции объектов, а компоненты отвечают за реализацию поведения.

Чаще всего используются типы компонентов [:::no-loc text="mesh components":::](meshes.md) , которые добавляют сетки в конвейер отрисовки. Аналогичным образом [компоненты освещения](../overview/features/lights.md) используются для добавления освещения, а [компоненты сечения](../overview/features/cut-planes.md) — для сечения открытых сеток.

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

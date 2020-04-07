---
title: Компоненты
description: Определение компонентов в области удаленного рендеринга Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681900"
---
# <a name="components"></a>Компоненты

Azure Remote Rendering использует шаблон [компонентной системы сущности.](https://en.wikipedia.org/wiki/Entity_component_system) В то время как [сущности](entities.md) представляют положение и иерархический состав объектов, компоненты отвечают за поведение реализации.

Наиболее часто используемыми типами компонентов являются [компоненты сетки,](meshes.md)которые добавляют сетки в конвейер рендеринга. Аналогичным образом, [световые компоненты](../overview/features/lights.md) используются для добавления освещения и [вырезанных компонентов плоскости](../overview/features/cut-planes.md) используются для разреза открытых сетк.

Все эти компоненты используют преобразование (положение, вращение, масштаб) сущности, к которой они прикреплены, в качестве точки отсчета.

## <a name="working-with-components"></a>Работа с компонентами

Вы можете легко добавлять, удалять и манипулировать компонентами программно:

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

Компонент прикрепляется к сущности во время создания. После этого он не может быть перемещен в другое существо. Компоненты явно удаляются с `Component.Destroy()` помощью или автоматически при уничтожении сущности владельца компонента.

Только один экземпляр каждого типа компонента может быть добавлен к сущности одновременно.

## <a name="unity-specific"></a>Специфическое единство

Интеграция Unity имеет дополнительные функции расширения для взаимодействия с компонентами. Смотрите [объекты и компоненты игры Unity.](../how-tos/unity/objects-components.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Границы объектов](object-bounds.md)
* [Сети](meshes.md)

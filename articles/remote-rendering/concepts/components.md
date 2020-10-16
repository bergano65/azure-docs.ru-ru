---
title: Components
description: Определение компонентов в области Удаленной отрисовки Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a488e2499b92b290ad2b55120c3c70a18d45d426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613933"
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

## <a name="api-documentation"></a>Документирование API

* [Компонентбасе C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# Ремотеманажер. Креатекомпонент ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [C# Entity. Финдкомпонентофтипе ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [Компонентбасе C++](https://docs.microsoft.com/cpp/api/remote-rendering/componentbase)
* [C++ Ремотеманажер:: Креатекомпонент ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createcomponent)
* [C++ Entity:: Финдкомпонентофтипе ()](https://docs.microsoft.com/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Дальнейшие действия

* [Границы объектов](object-bounds.md)
* [Сетки](meshes.md)

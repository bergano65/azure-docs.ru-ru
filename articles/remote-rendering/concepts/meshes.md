---
title: Сетки
description: Определение сеток в области удаленной подготовки к просмотру Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613834"
---
# <a name="meshes"></a>Сетки

## <a name="mesh-resource"></a>Ресурс сетки

Сетки — это неизменяемый [общий ресурс](../concepts/lifetime.md), который можно создать только с помощью [преобразования модели](../how-tos/conversion/model-conversion.md). Сетки содержат одну или несколько вложенных *сеток*. Каждая подсеть ссылается на [материал](materials.md) , по умолчанию который должен быть визуализирован. Чтобы поместить сетку в трехмерное пространство, добавьте [мешкомпонент](#meshcomponent) в [сущность](entities.md).

### <a name="mesh-resource-properties"></a>Свойства ресурса сетки

`Mesh`Свойства класса:

* **Материалы:** Массив материалов. Каждый материал используется другой подсетью. Несколько записей в массиве могут ссылаться на один и тот же [материал](materials.md). Эти данные невозможно изменить во время выполнения.

* **Границы:** Ограничивающий прямоугольник по оси локального пробела (ААББ) вершин сетки.

## <a name="meshcomponent"></a>мешкомпонент

`MeshComponent`Класс используется для размещения экземпляра ресурса сетки. Каждый Мешкомпонент ссылается на одну сеть. Он может переопределить, какие материалы используются для отрисовки каждой подсети.

### <a name="meshcomponent-properties"></a>Свойства Мешкомпонент

* **Сетка:** Ресурс сетки, используемый этим компонентом.

* **Материалы:** Массив материалов, указанный в самом компоненте сетки. Размер массива всегда будет такой же, как у массива *Materials* в ресурсе сетки. Материалы, которые не должны переопределяться из сетки по умолчанию, имеют значение *null* в этом массиве.

* **Уседматериалс:** Массив фактически используемых материалов для каждой подсети. Будет совпадать с данными в массиве *Materials* для значений, отличных от NULL. В противном случае он содержит значение из массива *Materials* в экземпляре сетки.

## <a name="api-documentation"></a>Документирование API

* [Класс сетки C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Класс C# Мешкомпонент](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Класс сетки C++](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [Класс C++ Мешкомпонент](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Дальнейшие действия

* [Материалы](materials.md)

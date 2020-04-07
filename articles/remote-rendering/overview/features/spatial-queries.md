---
title: Пространственные запросы
description: Как делать пространственные запросы в сцене
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680535"
---
# <a name="spatial-queries"></a>Пространственные запросы

Пространственные запросы — это операции, с помощью которых можно задать вопрос службе удаленного рендеринга, какие объекты находятся в той или иной области. Пространственные запросы часто используются для реализации взаимодействий, например, для определения объекта, на который указывает пользователь.

Все пространственные запросы оцениваются на сервере. Следовательно, это асинхронные операции, и результаты будут поступать с задержкой, которая зависит от задержки сети. Поскольку каждый пространственный запрос генерирует сетевой трафик, будьте осторожны, чтобы не сделать слишком много сразу.

## <a name="collision-meshes"></a>Столкновение сетки

Пространственные запросы питаются от двигателя [Havok Physics](https://www.havok.com/products/havok-physics) и требуют специальной сетки столкновения, чтобы присутствовать. По умолчанию [преобразование модели](../../how-tos/conversion/model-conversion.md) генерирует сетки коллизий. Если не требуется пространственных запросов на сложную модель, рассмотрите возможность отключения генерации сетки столкновений в [вариантах преобразования,](../../how-tos/conversion/configure-model-conversion.md)так как она оказывает несколько последствий:

* [Преобразование модели](../../how-tos/conversion/model-conversion.md) займет значительно больше времени.
* Размеры файлов преобразованной модели заметно больше, что влияет на скорость загрузки.
* Время выполнения загрузки больше.
* Потребление памяти процессора runtime выше.
* Для каждого экземпляра модели есть небольшие накладные расходы на производительность выполнения.

## <a name="ray-casts"></a>Рэй бросает

*Луч —* это пространственный запрос, в котором время выполнения проверяет, какие объекты пересекаются лучом, начиная с заданной позиции и указывая в определенном направлении. В качестве оптимизации дается также максимальное расстояние луча, чтобы не искать объекты, которые находятся слишком далеко.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Существует три режима сбора хитов:

* **Ближайший:** В этом режиме будет сообщено только самое близкое попадание.
* **Любой из:** Предпочитаю этот режим, когда все, что вы хотите знать, является *ли* луч будет хит ничего, но не волнует, что ударил точно. Этот запрос может быть значительно дешевле для оценки, но также имеет лишь несколько приложений.
* **Все:** В этом режиме, все хиты вдоль луча сообщаются, отсортированы на расстоянии. Не используйте этот режим, если вам действительно нужно больше, чем первый хит. Ограничьте количество зарегистрированных `MaxHits` показов опцией.

Чтобы исключить объекты выборочно из рассмотрения для лучевых слепков, можно использовать компонент [HierarchicalStateOverrideComponent.](override-hierarchical-state.md)

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Результат удара

Результатом запроса лучей является множество хитов. Массив пуст, если объект не был поражен.

Хит имеет следующие свойства:

* **Хит-лицо:** Какая [организация](../../concepts/entities.md) была поражена.
* **SubPartId:** Какой *подмеш был* поражен в [сетке.](../../concepts/meshes.md) Может быть использован `MeshComponent.UsedMaterials` для индексирования и поиска [материала](../../concepts/materials.md) в этой точке.
* **Хитпозиция:** Мировое космическое положение, где луч пересекобъект.
* **HitNormal:** Мировая космическая поверхность нормальной сетки в положении перекрестка.
* **РасстояниеToHit:** Расстояние от исходной позиции луча до удара.

## <a name="next-steps"></a>Дальнейшие действия

* [Границы объектов](../../concepts/object-bounds.md)
* [Переопределение иерархических состояний](override-hierarchical-state.md)

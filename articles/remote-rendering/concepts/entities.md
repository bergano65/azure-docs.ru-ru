---
title: Сущности
description: Определение сущностей в области API Удаленной отрисовки Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 5f6f7fc52a186117afcb92f6a2f80bf068e50ab9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509208"
---
# <a name="entities"></a>Сущности

*Сущность* представляет перемещаемый в пространстве объект и является базовым строительным блоком для любого содержимого удаленной отрисовки.

## <a name="entity-properties"></a>Свойства сущности

Для сущностей задаются преобразования со свойствами перемещения, поворота и масштабирования. Сами сущности не имеют никаких наблюдаемых возможностей. Любое поведение реализуется через добавление компонентов к сущностям. Например, добавив [CutPlaneComponent](../overview/features/cut-planes.md), можно создать плоскость разрезания в позиции расположения сущности.

Наиболее важным аспектом самой сущности является иерархия и итоговое иерархическое преобразование. Например, если несколько сущностей присоединены как дочерние элементы к одной родительской сущности, все эти сущности можно перемещать, поворачивать и масштабировать синхронно, изменяя преобразование для родительской сущности.

Сущность имеет строго один родительский элемент. При удалении родительского элемента с помощью `Entity.Destroy()` удаляются и все его дочерние элементы, в том числе подключенные [компоненты](components.md). Таким образом, модель из сцены можно удалить путем вызова `Destroy` для корневого узла модели, полученного от метода `AzureSession.Actions.LoadModelAsync()` или аналогичного метода SAS `AzureSession.Actions.LoadModelFromSASAsync()`.

Сущности создаются, когда сервер загружает содержимое или пользователь выражает намерение добавить объект в сцену. Например, если пользователь хочет добавить плоскость разрезания для визуализации внутренней части сетки, он может создать сущность в том положении, где должна располагаться эта плоскость, а затем добавить к ней компонент плоскости разрезания.

## <a name="create-an-entity"></a>Создание сущности

Чтобы добавить в сцену новую сущность, например для передачи ее в качестве корневого объекта для загрузки моделей или присоединения к нему компонентов, используйте следующий код:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->Position(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Функции запросов

В сущностях есть два типа функций запросов: синхронные и асинхронные вызовы. Синхронные запросы можно использовать только для тех данных, которые сохранены в клиенте и не требуют значительных вычислений. Например, к ним относится получение компонентов, относительных преобразований объектов или связей "родительский элемент — дочерний элемент". Асинхронные запросы используются для данных, существующих только на сервере или требующих дополнительных вычислений, для которых в клиенте недостаточно ресурсов. К ним относятся запросы пространственных границ или метаданных.

### <a name="querying-components"></a>Запросы для получения компонентов

Чтобы получить компонент определенного типа, используйте `FindComponentOfType`.

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = *entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Запросы для получения преобразований

Запросы преобразования выполняются синхронно и адресуются объекту. Важно отметить, что запрашиваемые через API преобразования являются локальными преобразованиями в пространстве относительно родителя объекта. Исключением можно считать корневые объекты, для которых локальное пространство и мировое пространство совпадают.

> [!NOTE]
> Не существует специального API для запроса преобразований произвольных объектов относительно мирового пространства.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = *entity->Position();
Quaternion rotation = *entity->Rotation();
```


### <a name="querying-spatial-bounds"></a>Запросы для получения пространственных границ

Запросы к границам выполняются асинхронно и могут применяться к полной иерархии объектов, где одна сущность считается корневой. Описанию границ объектов посвящена [отдельная глава](object-bounds.md) руководства.

### <a name="querying-metadata"></a>Запрос метаданных

Метаданными называются дополнительные данные, которые хранятся в объектах и игнорируются сервером. Метаданные объекта, по сути, представляют собой набор пар (имя и значение), где _значение_ может иметь числовой, логический или строковый тип. Метаданные можно экспортировать вместе с моделью.

Запросы к метаданным выполняются асинхронно и адресуются определенной сущности. Такой запрос возвращает метаданные только для одной сущности, но не объединяет их по вложенному графу.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->IsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = *query->Result();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->AsInt64();

            // ...
        }
    });
```

Запрос будет считаться успешно выполненным, даже если в объекте нет метаданных.

## <a name="next-steps"></a>Дальнейшие действия

* [Components](components.md)
* [Границы объектов](object-bounds.md)

---
title: Игровые объекты и компоненты Unity
description: Описывает специфические методы Unity для работы с объектами и компонентами удаленного рендеринга.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409858"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Взаимодействие с объектами и компонентами игр Unity

Удаленное рендерирование Azure (ARR) оптимизировано для огромного количества объектов (см. [Ограничения).](../../reference/limits.md) Хотя управление большими и сложными иерархиями на узлах возможно, их воспроизведение всех в Unity на маломощных устройствах невозможно.

Поэтому при загрузке модели на узел удаленная рендеринг Azure Remote Rendering отражает информацию о структуре модели на клиентском устройстве (которая будет нести сетевой трафик), но не реплицирует объекты и компоненты в Unity. Вместо этого он ожидает, что вы запросите нужные игровые объекты Unity и компоненты вручную, чтобы можно было ограничить накладные расходы тем, что на самом деле необходимо. Таким образом, у вас есть больше контроля над производительностью клиентской стороны.

Следовательно, интеграция Unity удаленного рендеринга Azure поставляется с дополнительной функциональностью для воспроизведения структуры удаленного рендеринга по требованию.

## <a name="load-a-model-in-unity"></a>Загрузите модель в Unity

При загрузке модели вы получаете ссылку на корневой объект загруженной модели. Эта ссылка не является игровым объектом Unity, но `Entity.GetOrCreateGameObject()`вы можете превратить его в один с помощью метода расширения. Эта функция ожидает аргумент `UnityCreationMode`типа . Если вы `CreateUnityComponents`пройдете, вновь созданный объект игры Unity будет дополнительно заполнен прокси-компонентами для всех компонентов удаленного рендеринга, которые существуют на хосте. Рекомендуется, однако, предпочитают, `DoNotCreateUnityComponents`чтобы сохранить накладные расходы минимальными.

### <a name="load-model-with-task"></a>Загрузка модели с задачей

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Модель загрузки с корутинами Unity

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Модель загрузки с режимом ожидания

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Приведенные выше образцы кода использовали траекторию загрузки модели через SAS, поскольку встроенная модель загружается. Обращение к модели с помощью `LoadModelAsync` `LoadModelParams`контейнеров с каплями (с использованием и) работает полностью аналогично.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Создание игрового объекта Unity `RemoteEntitySyncObject` неявно добавляет компонент в игровой объект. Этот компонент используется для синхронизации преобразования сущности на сервер. По `RemoteEntitySyncObject` умолчанию пользователь должен `SyncToRemote()` явно вызывать для синхронизации локального состояния Unity с сервером. Включение `SyncEveryFrame` синхронизирует объект автоматически.

Объекты `RemoteEntitySyncObject` с помощью бани имеют своих удаленных детей, мгновенно отображаемых и отображаемых в редакторе Unity через кнопку **Show Children.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Компоненты обертки

[Компоненты,](../../concepts/components.md) прикрепленные к объектам удаленного `MonoBehavior`рендеринга, подвергаются воздействию Unity через прокси-сервер s. Эти прокси представляют удаленный компонент в Unity и направляют все изменения в участок.

Для создания компонентов удаленного рендеринга прокси используйте метод `GetOrCreateArrComponent`расширения:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Соединенные жизни

Срок службы удаленной [сущности](../../concepts/entities.md) и объекта игры Unity связан, в то время как они связаны через `RemoteEntitySyncObject`. Если вы `UnityEngine.Object.Destroy(...)` позвоните с таким игровым объектом, удаленная сущность также будет удалена.

Чтобы уничтожить игровой объект Unity, не затрагивая `RemoteEntitySyncObject`удаленную сущность, сначала необходимо вызвать. `Unbind()`

То же самое относится ко всем прокси-компонентам. Чтобы уничтожить только представление на стороне клиента, сначала необходимо позвонить `Unbind()` по прокси-компоненту:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка Удаленной отрисовки для Unity](unity-setup.md)
* [Руководство. Работа с удаленными сущностями в Unity](../../tutorials/unity/working-with-remote-entities.md)

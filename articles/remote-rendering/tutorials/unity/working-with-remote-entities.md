---
title: Работа с удаленными сущностями в Unity
description: В этом руководстве показано, как работать с сущностями ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310211"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Руководство по Работа с удаленными сущностями в Unity

[Руководство. Настройка проекта Unity с нуля](project-setup.md) — вы узнали, как настроить новый проект Unity для работы с Удаленной отрисовкой Azure (ARR). В этом руководстве показаны распространенные функции, требуемые всем пользователям ARR.

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * выбор объекта с помощью лучей;
> * переопределение таких состояний объекта, как цвет оттенка, состояние выделения и видимость;
> * удаление удаленных сущностей;
> * перемещение удаленных сущностей;
> * использование плоскостей разрезания для просмотра содержимого объектов.

## <a name="prerequisites"></a>Предварительные требования

* Перед изучением этого руководства ознакомьтесь со статьей [Руководство. Настройка проекта Unity с нуля](project-setup.md).

> [!TIP]
> [Репозиторий примеров для службы "Удаленная отрисовка Azure"](https://github.com/Azure/azure-remote-rendering) содержит в папке *Unity* подготовленные проекты Unity для всех руководств, которые вы можете использовать как справочные пособия.

## <a name="pick-objects"></a>Выбор объектов

Мы хотим взаимодействовать с объектами, поэтому нам прежде всего нужна возможность выбирать объекты, расположенные под курсором мыши.

Создайте [новый скрипт](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) с именем **RemoteRaycaster** и замените все его содержимое следующим кодом:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Добавьте этот компонент в объект *RemoteRendering* в сцене.

> [!WARNING]
>
> Компонент *RemoteRaycaster* требует, чтобы к тому же объекту был присоединен компонент *ARRServiceUnity*. Вспомогательный класс *ARRServiceUnity* упрощает доступ к некоторым функциям ARR. Но в сцене может присутствовать только один экземпляр этого компонента. Поэтому все другие компоненты, которым также требуется *ARRServiceUnity*, следует добавлять в GameObject.
> Если вам нужен доступ к функциям ARR из нескольких объектов игры, можно добавить компонент *ARRServiceUnity* к только к одному из них, а в остальных скриптах использовать его по ссылке, или же напрямую обращаться к функциям ARR.

Нажмите кнопку воспроизведения, подключитесь к сеансу и загрузите модель. Теперь наведите указатель на любой объект в сцене и просмотрите выходные данные консоли. Здесь должны появляться имена объектов тех частей, на которые вы наводите курсор.

## <a name="highlight-objects"></a>Выделение объектов

Следующим шагом будет получение визуальной обратной связи для частей модели, на которые указывает пользователь. Для этого мы присоединим к выбранной сущности компонент [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md). Этот компонент можно использовать для включения или отключения разных признаков объекта. Здесь мы применим его для изменения цвета оттенка и включения [отрисовки структуры](../../overview/features/outlines.md).

Создайте новый файл скрипта с именем **RemoteModelEntity** и замените его содержимое следующим кодом:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Не присваивайте этот скрипт какому-либо игровому объекту, так как это будет выполнено программно с помощью следующего кода.

Теперь нам нужно расширить *RemoteRaycaster* путем добавления компонента *RemoteModelEntity* к выбранному объекту.

Добавьте в реализацию **RemoteRaycaster** следующий код, а затем удалите дублирующиеся функции:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Запустите проект и наведите указатель на любую часть модели, которая теперь должна стать красноватой с белым выделением структуры.

## <a name="isolate-the-selected-object"></a>Изоляция выделенного объекта

Еще одна полезная возможность [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) — переопределение видимости. Она позволяет вам изолировать выбранный объект от модели. Откройте скрипт **RemoteModelEntity**, добавьте в него следующий код, а затем удалите дублирующиеся функции:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Этот код зависит от наличия компонента, переопределяющего состояние, в верхнем объекте иерархии, который делает все объекты невидимыми. Затем этот код переопределяет состояние видимости для выбранного объекта, чтобы сделать его видимым. Это означает, что нам нужно создать компонент переопределения состояния в корневом объекте.

Откройте скрипт **RemoteRendering** и вставьте в приведенный ниже код в верхней части функции *LoadModel*:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

И наконец, нам нужен способ изменения видимости. Откройте скрипт **RemoteRaycaster** и замените в нем функцию *Update* следующим кодом:

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Снова выполните код и щелкните правой кнопкой мыши любую часть модели. При этом должна исчезнуть вся модель, кроме выделенного элемента.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Удаление экземпляров GameObject для удаленных сущностей

Вы могли заметить, что этот код постоянно создает объекты, но никогда не удаляет их. Это также заметно на панели иерархии объектов. По мере расширения иерархии удаленных объектов в ходе моделирования вы увидите, что при каждом наведении курсора на новую часть модели в ней появляются новые объекты.

Большое число объектов снижает производительность сцены. Поэтому вам следует всегда удалять неиспользуемые объекты.

Добавьте в скрипт **RemoteRaycaster** следующий код, а затем удалите дублирующиеся функции:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Перемещение объектов

Следующим шагом будет включение перемещения выделенного объекта. Добавьте в скрипт **RemoteRaycaster** следующий код, а затем удалите дублирующиеся функции:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Выполнив этот код, вы не увидите никаких изменений. Это связано с тем, что при преобразовании объекта сведения о его состоянии не синхронизируются на сервер автоматически, чтобы избежать проблем с производительностью. Поэтому вам нужно передавать состояние на сервер вручную или включить параметр **SyncEveryFrame** для компонента *RemoteEntitySyncObject*.

Откройте скрипт **RemoteModelEntity** и добавьте в него следующую строку:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Снова выполните этот код, и теперь вы сможете перетаскивать объект, щелкнув его левой кнопкой мыши.

## <a name="add-a-cut-plane"></a>Добавление плоскости разрезания

И в завершение работы с этим руководством мы включим поддержку [плоскостей разрезания](../../overview/features/cut-planes.md). Плоскость разрезания отделяет части отрисовываемых объектов, позволяя заглянуть внутрь этих объектов.

Создайте новый объект GameObject в сцене **CutPlane**. Создайте новый скрипт и присвойте ему имя **RemoteCutPlane**. Добавьте компонент к новому объекту GameObject.

Откройте файл этого скрипта и замените его содержимое следующим кодом.

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Теперь, выполнив этот код, вы увидите, как модель разрезается плоскостью. Для этого выберите объект *CutPlane*, чтобы перемещать и вращать его в пределах окна *Scene*. Плоскость разрезания можно отключить и включить обратно, изменяя активность соответствующего объекта.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы знаете, как работают самые важные функции для взаимодействия с удаленными объектами. В следующем руководстве мы перейдем к персонализации внешнего вида сцены.

> [!div class="nextstepaction"]
> [Руководство. Изменение среды и материалов](changing-environment-and-materials.md)

---
title: Изменение среды и материалов
description: В этом руководстве описано, как изменить карту неба и материалы объекта в сцене Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678685"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Руководство по Изменение среды и материалов

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Изменение карты среды сцены.
> * Изменение параметров материала.
> * Загрузка пользовательских текстур.

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что вы ознакомлены с [руководством по работе с удаленными сущностями в Unity](working-with-remote-entities.md). Но достаточно только проекта Unity, с помощью которого можно подключиться к сеансам и загрузить модель, как показано в [руководстве по настройке проекта Unity с нуля](project-setup.md).

> [!TIP]
> [Репозиторий примеров для службы "Удаленная отрисовка Azure"](https://github.com/Azure/azure-remote-rendering) содержит в папке *Unity* подготовленные проекты Unity для всех руководств, которые вы можете использовать как справочные пособия.

## <a name="change-the-environment-map"></a>Изменение карты среды

Служба "Удаленная отрисовка Azure" (ARR) поддерживает использование [скайбоксов](../../overview/features/sky.md) (иногда называемых "картами среды") для имитации освещения окружающей среды. Это особенно полезно в тех случаях, когда объекты используют *[физически корректную отрисовку](../../overview/features/pbr-materials.md)* , как модели в нашем примере. ARR также поставляется со множеством встроенных текстур неба, которые мы будем использовать при работе с этим руководством.

Создайте скрипт с именем **RemoteSky** и добавьте его в новый объект GameObject. Откройте файл этого скрипта и замените его содержимое следующим кодом:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Обратите внимание, что выше используется вариант `LoadTextureFromSASAsync`, так как загружаются встроенные текстуры. Если загрузка производится из [связанных хранилищ BLOB-объектов](../../how-tos/create-an-account.md#link-storage-accounts), используйте вариант `LoadTextureAsync`. Пример применения к моделям приведен в [разделе про загрузку моделей](../../concepts/models.md#loading-models).

При запуске кода и переключении карт неба вы сможете наблюдать значительное изменение освещения в вашей модели. При этом фон останется черным, и вы не увидите фактическую текстуру неба. Это сделано намеренно, так как визуализация фона будет отвлекающим фактором при использовании устройства дополненной реальности. В полноценном приложении используйте текстуры неба, похожие на реальное окружение, так как это поможет придать объектам более реалистичный вид.

## <a name="modify-materials"></a>Изменение материалов

При работе с предыдущим руководством мы использовали [компоненты переопределения состояния](../../overview/features/override-hierarchical-state.md) для изменения цвета оттенка выделенных объектов. Теперь мы хотим получить аналогичный результат, но путем изменения [материала](../../concepts/materials.md) объекта.

Сначала нам нужно создать скрипт для выбора объектов, как описано во [втором руководстве](working-with-remote-entities.md). Если у вас еще нет скрипта **RemoteRaycaster**, создайте его сейчас. Замените содержимое приведенным ниже кодом:

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
}
```

Добавьте компонент в игровой объект *RemoteRendering*. Он отвечает за выбор объектов, на которые наведен указатель мыши, и добавление компонентов *RemoteModelEntity* в выбранные объекты. Именно в этом классе компонента реализована функция изменения материала.

Если у вас еще нет скрипта **RemoteModelEntity**, создайте его и замените его содержимое следующим кодом:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

При выполнении этого кода объекты, на которые наведен указатель мыши, выделяются. Результат аналогичен тому, что мы получили в руководстве 2, но способ его достижения отличается. В этом случае мы получаем список материалов для выбранного объекта, а затем изменяем цвет альбедо первого из них.

> [!IMPORTANT]
> Обратите внимание, что этот метод будет выделять нужные части модели только в том случае, если она создана соответствующим образом. Он будет работать идеально, если для каждого объекта используется только один материал. Но если в модели отношение между частями и материалами не равно 1:1, то приведенный выше простой код не будет работать так, как нужно.

## <a name="use-a-different-texture"></a>Использование другой текстуры

[Текстуры](../../concepts/textures.md) обычно являются частью исходной модели. Во время [преобразования модели](../../quickstarts/convert-model.md) все текстуры преобразуются в необходимый формат среды выполнения и упаковываются в окончательный файл модели. Чтобы заменить текстуру во время выполнения, необходимо сохранить ее в [файле формата DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) и отправить в хранилище BLOB-объектов Azure. Дополнительные сведения о создании контейнера BLOB-объектов Azure см. в [этом кратком руководстве](../../quickstarts/convert-model.md). После создания контейнера BLOB-объектов его можно открыть в Обозревателе службы хранилища Azure и отправить файл с помощью перетаскивания.

На стороне среды выполнения к ресурсу текстуры в хранилище BLOB-объектов можно обращаться двумя разными способами:

* Обращение к текстуре по универсальному коду ресурса (URI) SAS. Для этого щелкните правой кнопкой мыши отправленный файл и выберите в контекстном меню пункт "**Получить подписанный URL-адрес...** ". Используйте этот универсальный код ресурса SAS с функцией `LoadTextureFromSASAsync` (см. пример кода ниже).
* Обращение к текстуре напрямую с помощью параметров хранилища BLOB-объектов, если [хранилище BLOB-объектов связано с учетной записью](../../how-tos/create-an-account.md#link-storage-accounts). В этом случае соответствующей функцией загрузки будет `LoadTextureAsync`.

Теперь откройте скрипт **RemoteModelEntity**, добавьте в него следующий код, а затем удалите дублирующиеся функции:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Запустите этот код и наведите указатель мыши на модель. Если у модели правильные координаты UV, должна отобразиться текстура. В противном случае вы сможете заметить только изменение цвета.

## <a name="next-steps"></a>Дальнейшие действия

На этом наша вводная серия руководств по использованию службы "Удаленная отрисовка Azure" с помощью Unity заканчивается. В качестве следующего этапа рекомендуем ознакомиться с некоторыми фундаментальными понятиями ARR, такими как [сеансы](../../concepts/sessions.md), [сущности](../../concepts/entities.md) и [модели](../../concepts/models.md), чтобы получить более глубокое понимание принципов работы. Рекомендуем также изучить такие функции, как [источники света](../../overview/features/lights.md), [отрисовка контуров](../../overview/features/outlines.md), [иерархические переопределения состояний](../../overview/features/override-hierarchical-state.md) и [материалы](../../concepts/materials.md).

> [!div class="nextstepaction"]
> [Игровые объекты и компоненты Unity](../../how-tos/unity/objects-components.md)

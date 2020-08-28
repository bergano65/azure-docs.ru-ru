---
title: Модели
description: Узнайте, что такое модель в Удаленной отрисовке Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9fd18195c0276999f445e0990838e293a5081131
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021890"
---
# <a name="models"></a>Модели

В Удаленной отрисовке Azure *моделью* называется полное представление объекта, составленное из [сущностей](entities.md) и [компонентов](components.md). Модели — это основной способ передачи пользовательских данных в службу удаленной отрисовки.

## <a name="model-structure"></a>Структура модели

У каждой модели только одна [сущность](entities.md) является корневым узлом. На следующих уровнях иерархии может существовать произвольная структура из дочерних сущностей. При загрузке модели возвращается ссылка на эту корневую сущность.

К каждой сущности могут прикрепляться [компоненты](components.md). В большинстве случаев сущности имеют компоненты *MeshComponents* со ссылками на [ресурсы сетки](meshes.md).

## <a name="creating-models"></a>Создание моделей

Создание моделей для среды выполнения достигается за счет [преобразования входных моделей](../how-tos/conversion/model-conversion.md) из файлов таких форматов, как FBX и GLTF. Процесс преобразования извлекает все ресурсы (текстуры, материалы, сетки и т. д.) и преобразует их в форматы, оптимизированные для среды выполнения. Он также извлекает структурную информацию и преобразует ее в структуру графа из сущностей и компонентов Удаленной отрисовки Azure.

> [!IMPORTANT]
>
> [Преобразование модели](../how-tos/conversion/model-conversion.md) — это единственный способ создания [сеток](meshes.md). В среде выполнения сетки могут совместно использоваться несколькими сущностями, но передать сетку в среду выполнения можно только путем загрузки модели.

## <a name="loading-models"></a>Загрузка моделей

После преобразования модель можно загрузить из хранилища BLOB-объектов Azure в среду выполнения.

Существуют две разные функции загрузки, которые отличаются способом адресации ресурса в хранилище BLOB-объектов.

* К модели можно обращаться по подписанному URL-адресу. Для этого используется функция `LoadModelFromSASAsync` с параметром `LoadModelFromSASParams`. Также используйте этот вариант для загрузки [встроенных моделей](../samples/sample-model.md).
* К модели можно обращаться напрямую по параметрам хранилища BLOB-объектов, если [хранилище BLOB-объектов связано с учетной записью](../how-tos/create-an-account.md#link-storage-accounts). В этом случае используется функция загрузки `LoadModelAsync` с параметром `LoadModelParams`.

В приведенных ниже фрагментах кода показано, как загрузить модели с помощью обеих этих функций. Чтобы загрузить модель по подписанному URL-адресу, используйте следующий код.

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Чтобы загрузить модель напрямую по ее параметрам в хранилище BLOB-объектов, используйте следующий код.

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

После этого вы сможете перемещаться по иерархии сущностей и изменять сущности и компоненты. Многократная загрузка одной и той же модели создает несколько экземпляров, каждый из которых содержит собственную копию структуры из сущностей и компонентов. Но сетки, материалы и текстуры являются [общими ресурсами](../concepts/lifetime.md), поэтому их данные не будут загружаться повторно. Это означает, что создание нескольких экземпляров модели потребляет ресурсы памяти незначительно.

> [!CAUTION]
> Все *асинхронные функции* в Удаленной отрисовке Azure возвращают объекты асинхронной операции. Ссылку на эти объекты необходимо хранить до завершения операции. В противном случае сборщик мусора C# может удалить операцию раньше, чем она завершится. Приведенный выше пример кода использует *await*, чтобы гарантировать сохранение ссылки в локальной переменной loadOp до полного завершения загрузки модели. Но если вместо этого используется событие *Completed*, асинхронную операцию необходимо хранить в переменной-члене.

## <a name="next-steps"></a>Дальнейшие действия

* [Сущности](entities.md)
* [Сетки](meshes.md)

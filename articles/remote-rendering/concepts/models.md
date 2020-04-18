---
title: Модели
description: Описывает, что представляет собой модель в удаленном рендере Зейнахе
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617950"
---
# <a name="models"></a>Модели

*Модель* удаленного рендеринга Azure относится к полному представлению объектов, состоящего из [сущностей](entities.md) и [компонентов.](components.md) Модели являются основным способом получения пользовательских данных в службу удаленного рендеринга.

## <a name="model-structure"></a>Структура модели

Модель имеет ровно одну [сущность](entities.md) в качестве корневого узла. Ниже, что он может иметь произвольной иерархии детских образований. При загрузке модели возвращается ссылка на эту корневую сущность.

Каждая сущность может иметь [компоненты,](components.md) прикрепленные. В наиболее распространенном случае, сущности имеют *MeshComponents*, которые ссылаются [на ресурсы сетки.](meshes.md)

## <a name="creating-models"></a>Создание моделей

Создание моделей для выполнения достигается путем [преобразования моделей ввода](../how-tos/conversion/model-conversion.md) из форматов файлов, таких как FBX и GLTF. Процесс преобразования извлекает все ресурсы, такие как текстуры, материалы и сетки, и преобразует их в оптимизированные форматы времени выполнения. Он также будет извлекать структурную информацию и преобразовывать ее в структуру диаграммы сущности/компонента ARR.

> [!IMPORTANT]
>
> [Преобразование модели](../how-tos/conversion/model-conversion.md) является единственным способом создания [сетки](meshes.md). Хотя сетки могут быть разделены между сущностями во время выполнения, нет другого способа получить сетку в время выполнения, кроме загрузки модели.

## <a name="loading-models"></a>Загрузка моделей

После преобразования модели ее можно загрузить из хранилища Azure blob в время выполнения.

Существуют две различные функции загрузки, которые отличаются тем, как актив рассматривается в хранилище капли:

* Модель может быть решена с помощью SAS URI. Соответствующая функция `LoadModelFromSASAsync` загрузки с параметром `LoadModelFromSASParams`. Используйте этот вариант также при [загрузке встроенных моделей.](../samples/sample-model.md)
* Модель может быть решена параметрами хранения капли непосредственно, в случае, если [хранилище капли связано с учетной записью.](../how-tos/create-an-account.md#link-storage-accounts) Соответствующая функция загрузки `LoadModelAsync` в `LoadModelParams`этом случае с параметром .

Следующие фрагменты кода показывают, как загрузить модели с любой функцией. Чтобы загрузить модель с помощью SAS URI, используйте код, как ниже:

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

Если вы хотите загрузить модель, непосредственно используя параметры хранения капли, используйте код, похожий на следующий фрагмент:

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

После этого можно пройти иерархию сущностей и изменить сущности и компоненты. Загрузка одной и той же модели несколько раз создает несколько экземпляров, каждый из которых имеет свою собственную копию структуры сущности/компонента. Поскольку сетки, материалы и текстуры являются [общими ресурсами,](../concepts/lifetime.md)их данные не будут загружены снова, однако. Поэтому мгновенное моделирование более одного раза несет относительно мало памяти накладных расходов.

> [!CAUTION]
> Все функции *Async* в ARR возвращают асинхронные объекты эксплуатации. Ссылка на эти объекты должна храниться до завершения операции. В противном случае сборщик мусора может удалить операцию досрочно, и она никогда не может закончиться. В примере кода выше использования *await* гарантирует, что локальная переменная 'loadOp' содержит ссылку до тех пор, пока загрузка модели не будет закончена. Однако, если вместо этого вы используете событие *Завершено,* необходимо хранить асинхронную операцию в переменной участника.

## <a name="next-steps"></a>Дальнейшие действия

* [Entities](entities.md)
* [Сетки](meshes.md)

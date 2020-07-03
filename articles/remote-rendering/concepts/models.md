---
title: Модели
description: Описывает модель в удаленной отрисовке Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617950"
---
# <a name="models"></a>Модели

*Модель* в удаленной отрисовке Azure — это полное представление объекта, состоящие из [сущностей](entities.md) и [компонентов](components.md). Модели — это основной способ получения пользовательских данных в службу удаленной отрисовки.

## <a name="model-structure"></a>Структура модели

В модели есть только одна [сущность](entities.md) в качестве корневого узла. Под ним может быть произвольная иерархия дочерних сущностей. При загрузке модели возвращается ссылка на эту корневую сущность.

К каждой сущности могут прикрепляться [компоненты](components.md) . В самом общем случае сущности имеют *мешкомпонентс*, которые ссылаются на [ресурсы сетки](meshes.md).

## <a name="creating-models"></a>Создание моделей

Создание моделей для среды выполнения достигается путем [преобразования входных моделей](../how-tos/conversion/model-conversion.md) из форматов файлов, таких как FBX и глтф. Процесс преобразования извлекает все ресурсы, такие как текстуры, материалы и сетки, и преобразует их в оптимизированные форматы времени выполнения. Он также извлекает структурную информацию и преобразует их в структуру графа сущностей и компонентов ARR.

> [!IMPORTANT]
>
> [Преобразование модели](../how-tos/conversion/model-conversion.md) — это единственный способ создания [сеток](meshes.md). Хотя сетки могут совместно использоваться сущностями во время выполнения, нет другого способа получить сетку в среде выполнения, отличной от загрузки модели.

## <a name="loading-models"></a>Загрузка моделей

После преобразования модель может быть загружена из хранилища BLOB-объектов Azure в среду выполнения.

Существует две отдельные функции загрузки, которые отличаются способом адресации ресурса в хранилище BLOB-объектов.

* К модели можно обращаться по URI SAS. Соответствующая функция загрузки `LoadModelFromSASAsync` связана с `LoadModelFromSASParams`параметром. Используйте этот вариант также при загрузке [встроенных моделей](../samples/sample-model.md).
* Вы можете напрямую обращаться к этой модели с помощью параметров хранилища BLOB-объектов, если оно [связано с учетной записью](../how-tos/create-an-account.md#link-storage-accounts). Соответствующая функция загрузки в этом случае `LoadModelAsync` — с `LoadModelParams`параметром.

В следующих фрагментах кода показано, как загружать модели с любой из функций. Чтобы загрузить модель с помощью URI SAS, используйте следующий код:

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

Если вы хотите загрузить модель напрямую с помощью ее параметров хранилища BLOB-объектов, используйте код, аналогичный приведенному ниже.

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

После этого можно перемещаться по иерархии сущностей и изменять сущности и компоненты. Многократная загрузка одной и той же модели создает несколько экземпляров, каждая из которых имеет собственную копию структуры сущности или компонента. Так как сетки, материалы и текстуры являются [общими ресурсами](../concepts/lifetime.md), их данные не будут загружаться повторно. Следовательно, создание экземпляра модели более чем один раз влечет за собой сравнительно мало ресурсов памяти.

> [!CAUTION]
> Все *асинхронные* функции в arr возвращают объекты асинхронной операции. Необходимо сохранить ссылку на эти объекты до завершения операции. В противном случае сборщик мусора на C# может удалить операцию на раннем этапе и не сможет завершиться. В примере кода выше использование параметра *await* гарантирует, что локальная переменная "лоадоп" содержит ссылку, пока не завершится загрузка модели. Однако, если бы вместо этого использовалось событие *Completed* , необходимо сохранить асинхронную операцию в переменной-члене.

## <a name="next-steps"></a>Дальнейшие шаги

* [Entities](entities.md)
* [Сетки](meshes.md)

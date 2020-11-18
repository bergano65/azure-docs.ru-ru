---
title: Включение управления версиями BLOB-объектов и работа с ним
titleSuffix: Azure Storage
description: Узнайте, как включить управление версиями BLOB-объектов в портал Azure или с помощью шаблона Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a52b736efaabdca8b08427f293ebf0cda5f22e44
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695883"
---
# <a name="enable-and-manage-blob-versioning"></a>Включение управления версиями BLOB-объектов и работа с ним

Вы можете включить управление версиями хранилища BLOB-объектов, чтобы автоматически поддерживать предыдущие версии объекта.  Если управление версиями BLOB-объектов включено, можно восстановить более раннюю версию большого двоичного объекта, чтобы восстановить данные, если они ошибочно изменены или удалены.

В этой статье показано, как включить или отключить управление версиями BLOB-объектов для учетной записи хранения с помощью портал Azure или шаблона Azure Resource Manager. Дополнительные сведения о управлении версиями BLOB-объектов см. в разделе [Управление версиями BLOB](versioning-overview.md)-объектов.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Включение управления версиями BLOB-объектов

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы включить управление версиями BLOB-объектов в портал Azure:

1. Перейдите к своей учетной записи хранения на портале.
1. В разделе **Служба BLOB-объектов** выберите **Защита данных**.
1. В разделе **Управление версиями** выберите **включено**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Снимок экрана, показывающий, как включить управление версиями BLOB-объектов в портал Azure":::

# <a name="template"></a>[Шаблон](#tab/template)

Чтобы включить управление версиями BLOB-объектов с помощью шаблона, создайте шаблон со свойством **исверсионинженаблед** в **значение true**. Следующие шаги описывают создание шаблона в портал Azure.

1. В портал Azure выберите **создать ресурс**.
1. В **поле Поиск в Marketplace** введите **шаблон развертывание** и нажмите клавишу **Ввод**.
1. Выберите **шаблоны развертывания**, выберите **создать**, а затем — создать **собственный шаблон в редакторе**.
1. В редакторе шаблонов вставьте следующий код JSON. Замените заполнитель `<accountName>` именем вашей учетной записи хранения.
1. при сохранении шаблона;
1. Укажите группу ресурсов учетной записи, а затем нажмите кнопку **приобрести** , чтобы развернуть шаблон и включить управление версиями BLOB-объектов.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Дополнительные сведения о развертывании ресурсов с помощью шаблонов в портал Azure см. в разделе [развертывание ресурсов с помощью портал Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Изменение большого двоичного объекта для активации новой версии

В следующем примере кода показано, как активировать создание новой версии с помощью клиентской библиотеки службы хранилища Azure для .NET версии [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) или более поздней. Перед запуском этого примера убедитесь, что вы включили управление версиями для учетной записи хранения.

В примере создается блочный BLOB-объект, а затем обновляются метаданные большого двоичного объекта. Обновление метаданных большого двоичного объекта инициирует создание новой версии. В примере извлекается исходная и текущая версии, и показывается, что метаданные содержат только текущая версия.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_TriggerNewBlobVersion":::

## <a name="list-blob-versions"></a>Вывод списка версий BLOB-объектов

Чтобы получить список версий BLOB-объектов или моментальных снимков с клиентской библиотекой .NET 12, укажите параметр [блобстатес](/dotnet/api/azure.storage.blobs.models.blobstates) в поле **Version** .

В следующем примере кода показано, как вывести версию BLOB-объектов с помощью клиентской библиотеки службы хранилища Azure для .NET версии [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) или более поздней. Перед запуском этого примера убедитесь, что вы включили управление версиями для учетной записи хранения.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Следующие шаги

- [Управление версиями BLOB-объектов](versioning-overview.md)
- [Soft delete for Azure Storage blobs](soft-delete-overview.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)

---
title: Выявление отключенных дисков Azure — портал Azure
description: Как найти отключенные управляемые и неуправляемые диски Azure (виртуальные жесткие диски и страничные BLOB-объекты) с помощью портала Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ad01155259ea03806f56c6e75b315ff85b606220
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302476"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Поиск и удаление отключенных управляемых и неуправляемых дисков Azure — портал Azure

Когда вы удаляете виртуальную машину в Azure, подключенные к ней диски не удаляются по умолчанию. Это предотвращает потерю данных из-за случайного удаления виртуальной машины. Тем не менее вы продолжаете платить за неподключенные диски. В этой статье показано, как найти и удалить все отключенные диски с помощью портала Azure, чтобы сократить затраты.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Управляемые диски. Поиск и удаление неподключенных дисков

Если у вас есть отключенные управляемые диски и данные на них больше не нужны, воспользуйтесь описанной ниже процедурой, чтобы найти их на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Найдите в поиске и выберите пункт **Диски**.

    В области **Диски** отображается список всех дисков. Все диски с пометкой **-** в столбце **Владелец** являются отключенными.

    [![](media/disks-find-unattached-portal/managed-disk-unattached-owner.png "Screenshot of the managed disks blade, if a disk has - in the Owner column, it is an unattached disk")](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Выберите отключенный диск, который нужно удалить. Откроется область этого диска.
1. В области диска можно еще раз проверить его состояние (отключен), а затем нажать **Удалить**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Снимок экрана: область отдельного управляемого диска. Если диск отключен, в этой области отображается состояние Отключен. Вы можете удалить этот диск, если данные на нем больше не нужны.":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Неуправляемые диски. Поиск и удаление неподключенных дисков

Неуправляемые диски — это VHD-файлы, которые хранятся в [учетных записях хранения Azure](../storage/common/storage-account-overview.md) как [страничные BLOB-объекты](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs).

Если у вас есть неуправляемые диски, которые не подключены к виртуальной машине и не содержат нужных данных, и вы хотите их удалить, воспользуйтесь описанной ниже процедурой, чтобы найти их на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Найдите в поиске и выберите пункт **Диски (классические)** .

    Появится список всех неуправляемых дисков. Все диски с пометкой **-** в столбце **Присоединено к** являются отключенными.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Снимок экрана: область неуправляемых дисков. Диски в этой области, для которых столбец Присоединено к содержит –, являются отключенными.":::

1. Выберите отключенный диск, который нужно удалить. Откроется область этого диска.

1. В области диска можно еще раз удостовериться, что он отключен, так как столбец **Присоединено к** будет по-прежнему содержать **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Снимок экрана: область отдельного неуправляемого диска. Если диск отключен, в столбце Присоединено к содержится –. Если данные на этом диске больше не нужны, его можно удалить.":::

1. Выберите команду **Удалить**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Снимок экрана: область отдельного неуправляемого диска с выделенной командой Удалить.":::

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите автоматизировать поиск и удаление неподключенных учетных записей хранения, ознакомьтесь со статьями о [CLI](linux/find-unattached-disks.md) и [PowerShell](windows/find-unattached-disks.md).

Дополнительные сведения см. в статье об [удалении учетной записи хранения](../storage/common/storage-account-create.md#delete-a-storage-account) и в записи блога о [выявлении потерянных дисков с помощью PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/).
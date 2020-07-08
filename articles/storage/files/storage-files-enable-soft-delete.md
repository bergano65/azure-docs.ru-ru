---
title: Включение обратимого удаления — общие папки Azure
description: Узнайте, как включить обратимое удаление (предварительная версия) в общих папках Azure для восстановления данных и предотвращения случайного удаления.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 84da8993bd1b0a61ef885d03f7c2bc80b92d47cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510812"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Включение обратимого удаления — общие папки Azure

Служба хранилища Azure теперь предоставляет возможность обратимого удаления (предварительная версия) для общих папок. Это упрощает восстановление данных, которые были случайно удалены приложением или другим пользователем учетной записи хранения. Дополнительные сведения об обратимом удалении см. в статье [Как предотвратить случайное удаление общих папок Azure](storage-files-prevent-file-share-deletion.md).

В следующих разделах показано, как включить обратимое удаление для общих папок Azure в существующей учетной записи хранения.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Перейдите к своей учетной записи хранения и выберите **Обратимое удаление** в разделе **Служба файлов**.
1. Выберите **включено** для параметра **обратимое удаление общих папок**.
1. Выберите **Срок хранения общей папки в днях** и введите число, которое вы выбрали.
1. Выберите **Сохранить**, чтобы подтвердить параметры хранения данных.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Снимок экрана: область параметров обратимого удаления учетной записи хранения. Выделите раздел общих папок, включите переключатель, задайте срок хранения и сохраните. Это приведет к включению обратимого удаления для всех общих папок в вашей учетной записи хранения.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы включить обратимое удаление, нужно обновить свойства службы клиента файлов. В следующем примере включено обратимое удаление для всех общих папок в учетной записи хранения:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Проверить, включено ли обратимое удаление, и просмотреть его политику хранения можно с помощью следующей команды:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Восстановление обратимо удаленных общих папок

# <a name="portal"></a>[Портал](#tab/azure-portal)

Для восстановление обратимо удаленной общей папки:

1. Перейдите к своей учетной записи хранения и выберите **Общие папки**.
1. В колонке общих папок включите **Показать удаленные общие папки**, чтобы отобразить все общие папки, которые были обратимо удалены.

    В результате будут отображены все общие папки, находящиеся в **удаленном** состоянии.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Если в столбце состояния рядом со столбцом имени установлено значение Удалено, то общая папка находится в обратимо удаленном состоянии. Такие папки будут окончательно удалены по истечении указанного срока хранения.":::

1. Выберите папку и щелкните **отменить удаление**. Это приведет к восстановлению общей папки.

    Вы можете подтвердить, что общая папка восстановлена, так как ее состояние меняется на **Активна**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Если в столбце Состояние рядом со столбцом имени установлено значение Активна, то общая папка была восстановлена.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы восстановить обратимо удаленную общую папку, используйте следующую команду:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Отключение обратимого удаления

Если вы хотите отменить использование обратимого удаления или окончательно удалить общую папку, выполните следующие действия.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Перейдите к своей учетной записи хранения и выберите **Обратимое удаление** в разделе **Параметры**.
1. В разделе **Общие папки** выберите **Отключено** для параметра **Обратимое удаление файловых ресурсов**.
1. Выберите **Сохранить**, чтобы подтвердить параметры хранения данных.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Отключение обратимого удаления позволит немедленно и окончательно удалить все общие папки в вашей учетной записи хранения, когда вам заблагорассудится.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы отключить обратимое удаление в учетной записи хранения, можно использовать следующую команду:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о другом способе защиты и восстановления данных в статье [Общие сведения о моментальных снимках общих ресурсов для Файлов Azure](storage-snapshots-files.md).

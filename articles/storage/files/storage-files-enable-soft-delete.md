---
title: Включение обратимого удаления — общие папки Azure
description: Узнайте, как включить обратимое удаление в общих папках Azure для восстановления данных и предотвращения случайного удаления.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126147"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Включение обратимого удаления — общие папки Azure

Служба хранилища Azure теперь предоставляет возможность обратимого удаления для общих папок. Это упрощает восстановление данных, если они ошибочно удалены приложением или другим пользователем учетной записи хранения. Дополнительные сведения об обратимом удалении см. в статье [Как предотвратить случайное удаление общих папок Azure](storage-files-prevent-file-share-deletion.md).

В следующих разделах показано, как включить обратимое удаление для общих папок Azure в существующей учетной записи хранения.

# <a name="portal"></a>[Портал](#tab/azure-portal)

## <a name="getting-started"></a>Начало работы

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Перейдите к своей учетной записи хранения и выберите **Обратимое удаление** в разделе **Служба файлов** .
1. Выберите **включено** для параметра **обратимое удаление общих папок** .
1. Выберите **Срок хранения общей папки в днях** и введите число, которое вы выбрали.
1. Выберите **Сохранить** , чтобы подтвердить параметры хранения данных.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Снимок экрана: область параметров обратимого удаления учетной записи хранения. Выделите раздел общих папок, включите переключатель, задайте срок хранения и сохраните. Это приведет к включению обратимого удаления для всех общих папок в вашей учетной записи хранения.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Предварительное требование

Командлеты обратимого удаления доступны в [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) версии модуля AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Начало работы с PowerShell

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

1. Перейдите к своей учетной записи хранения и выберите **Общие папки** .
1. В колонке общих папок включите **Показать удаленные общие папки** , чтобы отобразить все общие папки, которые были обратимо удалены.

    В результате будут отображены все общие папки, находящиеся в **удаленном** состоянии.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Снимок экрана: область параметров обратимого удаления учетной записи хранения. Выделите раздел общих папок, включите переключатель, задайте срок хранения и сохраните. Это приведет к включению обратимого удаления для всех общих папок в вашей учетной записи хранения.":::

1. Выберите папку и щелкните **отменить удаление** . Это приведет к восстановлению общей папки.

    Вы можете подтвердить, что общая папка восстановлена, так как ее состояние меняется на **Активна** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Снимок экрана: область параметров обратимого удаления учетной записи хранения. Выделите раздел общих папок, включите переключатель, задайте срок хранения и сохраните. Это приведет к включению обратимого удаления для всех общих папок в вашей учетной записи хранения.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Командлеты обратимого удаления доступны в 3.0.0 версии модуля AZ. Storage. Чтобы восстановить обратимо удаленную общую папку, используйте следующую команду:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Отключение обратимого удаления

Если вы хотите отменить использование обратимого удаления или окончательно удалить общую папку, выполните следующие действия.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Перейдите к своей учетной записи хранения и выберите **Обратимое удаление** в разделе **Параметры** .
1. В разделе **Общие папки** выберите **Отключено** для параметра **Обратимое удаление файловых ресурсов** .
1. Выберите **Сохранить** , чтобы подтвердить параметры хранения данных.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Снимок экрана: область параметров обратимого удаления учетной записи хранения. Выделите раздел общих папок, включите переключатель, задайте срок хранения и сохраните. Это приведет к включению обратимого удаления для всех общих папок в вашей учетной записи хранения.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Командлеты обратимого удаления доступны в 3.0.0 версии модуля AZ. Storage. Чтобы отключить обратимое удаление в учетной записи хранения, можно использовать следующую команду:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о другом способе защиты и восстановления данных в статье [Общие сведения о моментальных снимках общих ресурсов для Файлов Azure](storage-snapshots-files.md).

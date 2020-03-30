---
title: Проверьте состояние шифрования капли - Azure Storage
description: Узнайте, как использовать портал Azure, PowerShell или Azure CLI, чтобы проверить, зашифрован ли данный капли. Если капля не зашифрована, узнайте, как использовать AzCopy, чтобы заставить шифрование, загрузив и перезагрузив каплю.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707600"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Проверьте состояние шифрования капли

Каждый блок капли, капли приложения или капли страницы, которая была написана в Azure Storage после 20 октября 2017 года, зашифрован с помощью шифрования Azure Storage. Blobs, созданные до этой даты, продолжают шифроваться фоновым процессом.

В этой статье показано, как определить, был ли зашифрован данный blob.

## <a name="check-a-blobs-encryption-status"></a>Проверьте состояние шифрования капли

Используйте портал Azure, PowerShell или Azure CLI, чтобы определить, зашифрован ли капля без кода.

### <a name="azure-portal"></a>[Портал Azure](#tab/portal)

Чтобы использовать портал Azure, чтобы проверить, была ли зашифрована капля, выполните следующие действия:

1. Войдите в свою учетную запись хранения на портале Azure.
1. Выберите контейнеры для **навигации** по списку контейнеров в учетной записи.
1. Найдите каплю и отобразите его вкладку **Обзор.**
1. Просмотр **зашифрованного свойства сервера.** Если **True**, как показано на следующем изображении, то капля зашифрована. Обратите внимание, что свойства капли также включают дату и время создания капли.

    ![Скриншот, показывающий, как проверить зашифрованное свойство сервера на портале Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы использовать PowerShell, чтобы проверить, была ли зашифрована капля, проверьте **свойство IsServerEncrypted** от капли. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Чтобы определить, когда была создана капля, проверьте значение **созданного** свойства:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Лазурный CLI](#tab/cli)

Чтобы использовать Azure CLI для проверки того, была ли зашифрована капля, проверьте **свойство IsServerEncrypted** от капли. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Чтобы определить, когда была создана капля, проверьте стоимость **созданного** свойства.

---

## <a name="force-encryption-of-a-blob"></a>Принудительное шифрование капли

Если капля, созданная до 20 октября 2017 года, еще не зашифрована фоновым процессом, вы можете заставить шифрование произойти немедленно, загрузив и перезагрузив каплю. Простой способ сделать это с AzCopy.

Чтобы загрузить каплю в локальную файловую систему с помощью AzCopy, используйте следующий синтаксис:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Чтобы повторно загрузить каплю в Хранилище Azure с помощью AzCopy, используйте следующий синтаксис:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Для получения дополнительной информации об использовании AzCopy для копирования данных капли, [см.](../common/storage-use-azcopy-blobs.md)

## <a name="next-steps"></a>Дальнейшие действия

[Шифрование хранилища Azure для данных в состоянии покоя](../common/storage-service-encryption.md)

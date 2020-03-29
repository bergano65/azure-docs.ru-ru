---
title: Установка и управление политиками неизменяемости для хранения Blob - Azure Storage
description: Узнайте, как использовать поддержку WORM (Write Once, Read Many) для хранения данных в неизменяемом, неизменяемом состоянии для определенного интервала.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970104"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Установка и управление политиками неизменяемости для хранения Blob

Неизменяемое хранилище для хранения Azure Blob позволяет пользователям хранить критически важные для бизнеса объекты данных в состоянии WORM (Write Once, Read Many). Это состояние делает их нестираемыми и неизменяемыми в течение определенного пользователем интервала времени. В течение интервала удержания капли могут быть созданы и прочитаны, но не могут быть изменены или удалены. Неизменяемое хранилище доступно для учетных записей v2 и Blob общего назначения во всех регионах Azure.

В этой статье показано, как устанавливать и управлять политиками неизменяемости и законными запасами данных в хранилище Blob с помощью портала Azure, PowerShell или Azure CLI. Для получения дополнительной информации о [Store business-critical blob data with immutable storage](storage-blob-immutable-storage.md)неизменяемом хранилище см.

## <a name="set-retention-policies-and-legal-holds"></a>Установка политик удержания и правовых удержаний

### <a name="portal"></a>[Портал](#tab/azure-portal)

1. Создайте новый контейнер или выберите существующий контейнер для хранения BLOB-объектов, которые должны храниться в неизменяемом состоянии. Контейнер должен быть в общеспециальном счете хранения v2 или Blob.

2. В параметрах контейнера выберите **Политика доступа**. Затем выберите **Политику добавить** под **immutable хранилище капли.**

    ![Параметры контейнера на портале](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Чтобы включить временный период удержания, в раскрывающемся меню выберите **Хранение с учетом времени**.

    ![Параметр "Хранение с учетом времени", выбранный в разделе типа политики](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Введите интервал удержания в днях (приемлемые значения от 1 до 146000 дней).

    ![Поле "Изменить срок хранения на"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Начальное состояние политики разблокировано, что позволяет протестировать функцию и внести изменения в политику, прежде чем заблокировать ее. Блокировка политики имеет важное значение для соблюдения таких правил, как SEC 17a-4.

5. Заблокируйте политику. Справа щелкните эллипсис **(...**), и следующее меню появляется с дополнительными действиями:

    ![Параметр "Политика блокировки" в меню](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Выберите **политику блокировки** и подтвердите блокировку. Политика теперь заблокирована и не может быть удалена, будут разрешены только расширения интервала удержания. Blob удаляет и переопределяет не допускается. 

    ![Подтвердите "Блокировка политики" в меню](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Для обеспечения юридических удержаний выберите **Политику добавления.** В раскрывающемся меню выберите **Удержание по юридическим причинам**.

    ![Параметр в меню "Удержание по юридическим причинам" под полем "Тип политики"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Создайте юридическое удержание с одним или несколькими тегами.

    ![Поле "Имя тега" под полем "Тип политики"](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Чтобы очистить юридический удержание, удалите применяемый правовой тег идентификатора удержания.

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Эта возможность доступна в следующих группах команд: `az storage container immutability-policy` и `az storage container legal-hold`. Запустите в них `-h`, чтобы просмотреть команды.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Модуль Az.Storage поддерживает неизменяемое хранилище.  Чтобы включить эту функцию, сделайте следующее:

1. Установите последнюю версию PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Удалите все предыдущие версии Azure PowerShell.
3. Установка Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Пример сценария PowerShell ниже приводится для справки. Он создает учетную запись хранения и контейнер. Затем он показывает, как установить и очистить юридические удержания, создать и заблокировать временную политику удержания (также известную как политика неизменности), и расширить интервал удержания.

Во-первых, создайте учетную запись хранения Azure:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Установка и удаление юридических удержаний:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Создание или обновление политик инеменяемости на основе времени:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Извлечение политик неизменяемости:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Блокировка политики неизменяемости (добавить, `-Force` чтобы уволить запрос):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Расширение политик неизменяемости:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Удалите разблокированную политику `-Force` неизменяемости (добавить, чтобы отклонить запрос):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Включение позволяет защищенным приложениям

### <a name="portal"></a>[Портал](#tab/azure-portal)

![Разрешить дополнительные записи приложения](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Эта возможность доступна в следующих группах команд: `az storage container immutability-policy` и `az storage container legal-hold`. Запустите в них `-h`, чтобы просмотреть команды.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Дальнейшие действия

[Храните критически важные для бизнеса данные с непреложным хранилищем](storage-blob-immutable-storage.md)

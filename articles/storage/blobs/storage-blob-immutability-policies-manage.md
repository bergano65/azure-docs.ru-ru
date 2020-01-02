---
title: Настройка и управление политиками неизменности для хранилища BLOB-объектов в службе хранилища Azure
description: Узнайте, как использовать ЧЕРВи (однократная запись, чтение и поддержка) для хранения больших двоичных объектов (объектов) для хранения данных в неизменяемом состоянии, не допускающем изменения, в течение указанного интервала.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 79d7454722900eb1d9d6280e35313ef2f4a5cd54
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555682"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Настройка политик неизменности для хранилища BLOB-объектов и управление ими

Неизменяемое хранилище для хранилища BLOB-объектов Azure позволяет пользователям хранить критически важные для бизнеса объекты данных в ЧЕРВе (однократная запись, считывание из множества). Это состояние делает их нестираемыми и неизменяемыми в течение определенного пользователем интервала времени. В течение интервала хранения большие двоичные объекты можно создавать и читать, но нельзя изменять или удалять. Неизменяемое хранилище доступно для учетных записей общего назначения версии 2 и хранилища BLOB-объектов во всех регионах Azure.

В этой статье показано, как задать и управлять политиками неизменности и юридическими удержаниями для данных в хранилище BLOB-объектов с помощью портал Azure, PowerShell или Azure CLI. Дополнительные сведения о неизменяемом хранилище см. [в статье хранение критически важных бизнес-данных с помощью неизменяемого хранилища](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Настройка политик хранения и юридических удержаний

### <a name="portaltabazure-portal"></a>[Microsoft Azure](#tab/azure-portal)

1. Создайте новый контейнер или выберите существующий контейнер для хранения BLOB-объектов, которые должны храниться в неизменяемом состоянии. Контейнер должен быть в учетной записи общего назначения версии 2 или хранилища BLOB-объектов.

2. В параметрах контейнера выберите **Политика доступа**. Затем выберите **Добавить политику** в **неизменяемом хранилище BLOB-объектов**.

    ![Параметры контейнера на портале](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Чтобы включить временный период удержания, в раскрывающемся меню выберите **Хранение с учетом времени**.

    ![Параметр "Хранение с учетом времени", выбранный в разделе типа политики](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Введите интервал хранения в днях (допустимые значения: от 1 до 146000 дней).

    ![Поле "Изменить срок хранения на"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Начальное состояние политики разблокируется, что позволяет протестировать функцию и внести изменения в политику перед ее блокировкой. Блокировка политики необходима для обеспечения соответствия нормативным требованиям, таким как SEC 17A-4.

5. Заблокируйте политику. Щелкните правой кнопкой мыши кнопку с многоточием ( **...** ) и в открывшемся меню появятся дополнительные действия.

    ![Параметр "Политика блокировки" в меню](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Выберите **блокировать политику** и подтвердите блокировку. Политика заблокирована и не может быть удалена, только расширения для интервала хранения будут разрешены. Удаление и переопределение BLOB-объектов не разрешено. 

    ![Подтвердите "заблокировать политику" в меню.](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Чтобы включить юридические удержания, выберите **Добавить политику**. В раскрывающемся меню выберите **Удержание по юридическим причинам**.

    ![Параметр в меню "Удержание по юридическим причинам" под полем "Тип политики"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Создайте юридическое удержание с одним или несколькими тегами.

    ![Поле "Имя тега" под полем "Тип политики"](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Чтобы очистить юридическую силу, просто удалите примененный тег "идентификатор юридического удержания".

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Эта возможность доступна в следующих группах команд: `az storage container immutability-policy` и `az storage container legal-hold`. Запустите в них `-h`, чтобы просмотреть команды.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Модуль AZ. Storage поддерживает неизменяемое хранилище.  Чтобы включить эту функцию, сделайте следующее:

1. Установите последнюю версию PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Удалите все предыдущие версии Azure PowerShell.
3. Установка Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Пример сценария PowerShell ниже приводится для справки. Он создает учетную запись хранения и контейнер. Далее в нем показано, как задавать и снимать юридические удержания, создавать и блокировать политику хранения на основе времени (известную как неизменяемая политика), а также продлевать интервал хранения.

Сначала создайте учетную запись хранения Azure:

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

Создание или обновление политик неизменяемости:

```powershell
# Create an immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Извлечение политик неизменяемости:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Блокировать политики неизменности (добавьте `-Force`, чтобы закрыть запрос):

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

Удалите незаблокированную политику неизменности (добавьте `-Force`, чтобы закрыть запрос):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="next-steps"></a>Дальнейшие действия

[Хранение критически важных для бизнеса данных большого двоичного объекта с неизменяемым хранилищем](storage-blob-immutable-storage.md)
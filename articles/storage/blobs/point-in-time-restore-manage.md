---
title: Включение и Управление восстановлением на момент времени для блочных BLOB-объектов (Предварительная версия)
titleSuffix: Azure Storage
description: Узнайте, как использовать восстановление до точки во времени (Предварительная версия) для восстановления блочных BLOB-объектов в состояние на более ранний момент времени.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118216"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Включение и Управление восстановлением на момент времени для блочных BLOB-объектов (Предварительная версия)

Вы можете использовать восстановление на момент времени (Предварительная версия) для восстановления блочных BLOB-объектов в свое состояние в более ранней момент времени. В этой статье описывается, как включить восстановление на момент времени для учетной записи хранения с помощью PowerShell. В нем также показано, как выполнить операцию восстановления с помощью PowerShell.

Дополнительные сведения и сведения о регистрации в предварительной версии см. в разделе [Восстановление до точки во времени для блочных BLOB-объектов (Предварительная версия)](point-in-time-restore-overview.md).

> [!CAUTION]
> Восстановление до точки во времени поддерживает операции восстановления только для блочных BLOB-объектов. Операции с контейнерами не могут быть восстановлены. Если удалить контейнер из учетной записи хранения, вызвав операцию [удаления контейнера](/rest/api/storageservices/delete-container) во время предварительной версии восстановления на момент времени, этот контейнер нельзя будет восстановить с помощью операции восстановления. При предварительном просмотре вместо удаления контейнера Удалите отдельные большие двоичные объекты, если их потребуется восстановить.

> [!IMPORTANT]
> Предварительная версия восстановления на момент времени предназначена только для использования в рабочей среде. Соглашения об уровне обслуживания (SLA) для рабочих сред сейчас недоступны.

## <a name="install-the-preview-module"></a>Установка модуля предварительной версии

Чтобы настроить восстановление на момент времени Azure с помощью PowerShell, сначала установите версию [1.14.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) модуля AZ. Storage PowerShell. Выполните следующие действия, чтобы установить модуль предварительной версии:

1. Удалите все предыдущие установки Azure PowerShell из Windows с помощью параметра **приложения & компоненты** в разделе **Параметры**.

1. Убедитесь, что у вас установлена последняя версия PowerShellGet. Откройте окно Windows PowerShell и выполните следующую команду, чтобы установить последнюю версию:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. После установки PowerShellGet закройте и снова откройте окно PowerShell.

1. Установите Azure PowerShell последней версии:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Установите модуль AZ. Storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Дополнительные сведения об установке Azure PowerShell см. в [статье установка Azure PowerShell с помощью PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Включение и Настройка восстановления на момент времени

Перед включением и настройкой восстановления на момент времени включите необходимые компоненты: обратимое удаление, веб-канал изменений и управление версиями BLOB-объектов. Дополнительные сведения о включении каждой из этих функций см. в следующих статьях:

- [Включить обратимое удаление для больших двоичных объектов](soft-delete-enable.md)
- [Включение и отключение веб-канала изменений](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Включение управления версиями BLOB-объектов и управление ими](versioning-enable.md)

Чтобы настроить восстановление на момент времени Azure с помощью PowerShell, вызовите команду Enable-Азсторажеблобрестореполици. В следующем примере активируется обратимое удаление и устанавливается срок хранения обратимого удаления, включается веб-канал изменений, а затем включается восстановление на момент времени. Перед выполнением примера используйте портал Azure или шаблон Azure Resource Manager, чтобы включить управление версиями BLOB-объектов.

При выполнении примера не забудьте заменить значения в угловых скобках собственными значениями:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Выполнение операции восстановления

Чтобы инициировать операцию восстановления, вызовите команду RESTORE-Азсторажеблобранже, указав точку восстановления как значение **DateTime** в формате UTC. Можно указать один или несколько лексикографическом диапазонов больших двоичных объектов для восстановления или опустить диапазон для восстановления всех больших двоичных объектов во всех контейнерах в учетной записи хранения. Выполнение операции восстановления может занять несколько минут.

При указании диапазона больших двоичных объектов для восстановления учитывайте следующие правила.

- Шаблон контейнера, указанный для начального диапазона и конечного диапазона, должен включать не менее трех символов. Косая черта (/), используемая для разделения имени контейнера из имени большого двоичного объекта, не учитывает этот минимум.
- Для каждой операции восстановления можно указать только один диапазон.
- Подстановочные знаки не поддерживаются. Они рассматриваются как стандартные символы.
- Вы можете восстановить большие двоичные объекты в `$root` `$web` контейнерах и, явно указав их в диапазоне, переданном в операцию восстановления. `$root` `$web` Контейнеры и восстанавливаются только в том случае, если они заданы явным образом. Не удается восстановить другие системные контейнеры.

### <a name="restore-all-containers-in-the-account"></a>Восстановить все контейнеры в учетной записи

Чтобы восстановить все контейнеры и большие двоичные объекты в учетной записи хранения, вызовите команду RESTORE-Азсторажеблобранже, опустив `-BlobRestoreRange` параметр. В следующем примере контейнеры в учетной записи хранения восстанавливаются в состояние 12 часов до текущего момента:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Восстановление одного диапазона блочных BLOB-объектов

Чтобы восстановить диапазон больших двоичных объектов, вызовите команду RESTORE-Азсторажеблобранже и укажите диапазон лексикографическом для имени контейнера и большого двоичного объекта для `-BlobRestoreRange` параметра. Начало диапазона является инклюзивным, а конец диапазона — эксклюзивным.

Например, чтобы восстановить большие двоичные объекты в одном контейнере с именем *Sample-Container*, можно указать диапазон, который начинается с *Sample-Container* и оканчивается на *Sample-container1*. Не требуется существование контейнеров с именами в начальном и конечном диапазонах. Так как конец диапазона является эксклюзивным, даже если учетная запись хранения содержит контейнер с именем *Sample-container1*, будут восстановлены только контейнер с именем *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Чтобы указать подмножество больших двоичных объектов в контейнере для восстановления, используйте косую черту (/), чтобы отделить имя контейнера от шаблона большого двоичного объекта. Например, следующий диапазон выбирает большие двоичные объекты в одном контейнере, имена которых начинаются с букв *d* по *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Затем укажите диапазон для команды Restore-Азсторажеблобранже. Укажите точку восстановления, указав значение **даты и времени** в формате UTC для `-TimeToRestore` параметра. В следующем примере выполняется восстановление больших двоичных объектов в указанном диапазоне до текущего момента в течение 3 дней:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Восстановление нескольких диапазонов блочных BLOB-объектов

Чтобы восстановить несколько диапазонов блочных BLOB-объектов, укажите массив диапазонов для `-BlobRestoreRange` параметра. В следующем примере восстанавливается полное содержимое *container1* и *container4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Дальнейшие действия

- [Восстановление до точки во времени для блочных BLOB-объектов (Предварительная версия)](point-in-time-restore-overview.md)
- [Обратимое удаление](soft-delete-overview.md)
- [Веб-канал изменений (Предварительная версия)](storage-blob-change-feed.md)
- [Управление версиями BLOB-объектов (Предварительная версия)](versioning-overview.md)

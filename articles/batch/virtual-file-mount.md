---
title: Установите виртуальную файловую систему в пул - Azure Batch Документы Майкрософт
description: Узнайте, как установить виртуальную файловую систему в пуле пакетов.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919011"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Установите виртуальную файловую систему в пул пакетов

Azure Batch теперь поддерживает монтаж облачного хранилища или внешнюю файловую систему на вычислительных узлах Windows или Linux в пулах пакетов. Когда вычислительный узла присоединяется к пулу, виртуальная файловая система монтируется и рассматривается как локальный диск на этом уде. Можно монтировать файлохранилища, такие как Azure Files, хранилище Azure Blob, сетевая файловая система (NFS), включая [кэш Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)или общую систему файлов Интернета (CIFS).

В этой статье вы узнаете, как установить виртуальную файловую систему на пул вычислительных узлов, используя [библиотеку управления пакетами для .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)

> [!NOTE]
> Установка виртуальной файловой системы поддерживается в пулах пакетов, созданных на 2019-08-19 или после. Пакетные пулы, созданные до 2019-08-19, не поддерживают эту функцию.
> 
> AA для монтажа файловых систем на вычислительном узлах являются частью библиотеки [Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Преимущества монтажа на бассейне

Установка файловой системы в пул вместо того, чтобы позволять задачам получать свои собственные данные из большого набора данных, упрощает и эффективнее выполнять задачи для доступа к необходимым данным.

Рассмотрим сценарий с несколькими задачами, требующими доступа к общему набору данных, например рендерингу фильма. Каждая задача отображает один или несколько кадров одновременно из файлов сцены. Устанавливая диск, содержащий файлы сцены, вычислительным узлам проще получить доступ к общим данным. Кроме того, базовая файловая система может быть выбрана и масштабирована независимо на основе производительности и масштаба (пропускная способность и IOPS), требуемого количеством вычислительных узлов, одновременно попадающих к данным. Например, распределенный кэш [памяти Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) может использоваться для поддержки больших реаниматозных рендеров с тысячами одновременных узлов рендеринга, доступа к исходным данным, которые находятся в помещениях. Кроме того, для данных, которые уже находятся в облачном хранилище Blob, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) может быть использован для установки этих данных в качестве локальной файловой системы. Blobfuse доступен только на узлах Linux, [однако, Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) обеспечивает аналогичный рабочий процесс и доступен как на Windows, так и на Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Установите виртуальную файловую систему в пул  

Установка виртуальной файловой системы в пул ею делает файловую систему доступной для каждого вычислительного узла в пуле. Файловая система настраивается либо при присоединении вычислительного узла к пулу, либо при перезапущени или перемазании узла.

Чтобы установить файловую систему `MountConfiguration` в пул, создайте объект. Выберите объект, который соответствует `AzureBlobFileSystemConfiguration`вашей виртуальной файловой системе: , `AzureFileShareConfiguration`, `NfsMountConfiguration`или `CifsMountConfiguration`.

Все объекты конфигурации крепления нуждаются в следующих базовых параметрах. Некоторые конфигурации крепления имеют параметры, характерные для используемой файловой системы, которые более подробно обсуждаются в примерах кода.

- **Имя учетной записи или источник**: Чтобы установить виртуальную долю файла, вам нужно имя учетной записи хранилища или ее источник.
- **Относительная траектория крепления или Источник**: Расположение файловой системы, `fsmounts` установленной на вычислительном `AZ_BATCH_NODE_MOUNTS_DIR`узлах, по отношению к стандартному каталогу, доступному на уде через. Точное местоположение варьируется в зависимости от операционной системы, используемой на уде. Например, физическое местоположение на уиде Ubuntu отображается на `mnt\batch\tasks\fsmounts`, и `mnt\resources\batch\tasks\fsmounts`на уде CentOS он отображается на .
- **Параметры крепления или параметры блоббза:** Эти параметры описывают конкретные параметры для монтажа файловой системы.

После `MountConfiguration` создания объекта при создании пула `MountConfigurationList` привязании объекта к свойству присваивайте объект. Файловая система устанавливается либо при присоединении узла к пулу, либо при перезапущени или перемазании узла.

При установке файловой системы `AZ_BATCH_NODE_MOUNTS_DIR` создается переменная среды, которая указывает на расположение установленных файловых систем, а также файлов журнала, которые полезны для устранения неполадок и отладки. Файлы журнала объясняются более подробно в разделе [ошибок крепления Diagnose.](#diagnose-mount-errors)  

> [!IMPORTANT]
> Максимальное количество установленных файловых систем в пуле составляет 10. Просмотрите [квоты и лимиты служб пакетов](batch-quota-limit.md#other-limits) для деталей и других ограничений.

## <a name="examples"></a>Примеры

Следующие примеры кода демонстрируют установку различных файлов в пул вычислительных узлов.

### <a name="azure-files-share"></a>Совместное с файлами Azure

Azure Files — это стандартная облачная файловая система Azure. Подробнее о том, как получить любой из параметров в примере кода конфигурации крепления, можно узнать, как получить [общий образец кода конфигурации крепления, см.](../storage/files/storage-how-to-use-files-windows.md)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Файловая система Azure Blob

Другой вариант заключается в использовании Azure Blob хранения через [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Установка файловой системы `AccountKey` blob требует или `SasKey` для вашей учетной записи хранения. Для получения информации о получении этих ключей [см. Управляйте ключами доступа к учетной записи хранения](../storage/common/storage-account-keys-manage.md)или [с помощью общих подписей доступа (SAS).](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Для получения дополнительной информации об использовании блобью, см Blobfuse [Troubleshoot часто задаваемые вопросы](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Чтобы получить доступ по умолчанию к каталогу, установленной блобфузом, запустите задачу в качестве **администратора.** Blobfuse монтирует каталог в пользовательском пространстве, а при создании пула он монтируется как корень. В Linux все задачи **Администратора** являются корневыми. Все варианты модуля FUSE описаны на [странице ссылки FUSE.](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)

В дополнение к руководству по устранению неполадок, gitHub вопросы в хранилище блобфуза являются полезным способом проверить текущие проблемы и разрешения блоббайс. Для получения дополнительной информации, см [Blobfuse вопросы](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS

Network File Systems (NFS) также может быть установлен для объединения узлов, позволяющих легко получить доступ к традиционным файловым системам с помощью узлов Azure Batch. Это может быть один сервер NFS, развернутый в облаке, или внутренний сервер NFS, доступ к нему через виртуальную сеть. Кроме того, воспользуйтесь распределенным решением [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) в кэше памяти, которое обеспечивает беспрепятственное подключение к хранению на местах, считывание данных по требованию в кэше и обеспечивает высокую производительность и масштабирование облачных вычислительных узлов.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Общая система файлов Интернета

Общие системы файлов Интернета (CIFS) также могут быть установлены для объединения узлов, позволяющих легко получить доступ к традиционным файловым системам с помощью узлов Azure Batch. CIFS — это протокол обмена файлами, который обеспечивает открытый и кросс-платформенный механизм запроса файлов и служб сетевого сервера. CIFS основан на расширенной версии протокола Microsoft Server Message Block (SMB) для обмена файлами в Интернете и интрасети и используется для установки внешних файловых систем на узлах Windows. Чтобы узнать больше о SMB, [см.](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Диагностика ошибок крепления

При сбою конфигурации крепления вычислительный узла в пуле выйдет из строя, а состояние узла становится непригодным для эксплуатации. Чтобы диагностировать сбой конфигурации крепления, проинспектировать свойство [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) для получения подробной информации об ошибке.

Чтобы получить файлы журнала для отладки, `*.log` используйте [OutputFiles](batch-task-output-files.md) для загрузки файлов. Файлы `*.log` содержат информацию о том, что файловая система монтируется в `AZ_BATCH_NODE_MOUNTS_DIR` этом месте. Файлы журнала Mount `<type>-<mountDirOrDrive>.log` имеют формат: для каждого крепления. Например, `cifs` крепление в каталоге `test` крепления с именем `cifs-test.log`будет иметь файл журнала крепления с именем: .

## <a name="supported-skus"></a>Поддерживаемые номера SKU

| Издатель | ПРЕДЛОЖЕНИЕ | номер SKU | Совместное с файлами Azure | Blobfuse | Гора NFS | CIFS крепления |
|---|---|---|---|---|---|---|
| пакет (1) | rendering-centos73 | rendering | :heavy_check_mark: <br>Примечание: Совместимо с CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Примечание: Совместимо с CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Примечание: поддерживает A_8 или 9 хранения</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle; | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о том, как совместно с [Windows](../storage/files/storage-how-to-use-files-windows.md) или [Linux](../storage/files/storage-how-to-use-files-linux.md)можно ознакомиться с файлами Azure.
- Узнайте об использовании и монтаже виртуальных файловых систем [blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Смотрите [обзор сетевой файловой системы,](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) чтобы узнать о NFS и ее приложениях.
- [Подробнее](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) о CIFS можно узнать больше о CIFS.

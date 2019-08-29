---
title: Подключение виртуальной файловой системы к пулу (пакетная служба Azure) | Документация Майкрософт
description: Узнайте, как подключить виртуальную файловую систему к пулу пакетной службы.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.openlocfilehash: 1c990c864f9daa98460832166b31f43fece1ed15
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093854"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Подключение виртуальной файловой системы в пуле пакетной службы

Пакетная служба Azure теперь поддерживает подключение облачного хранилища или внешней файловой системы на вычисленных узлах Windows или Linux в пулах пакетной службы. Когда кластерный узел присоединяется к пулу, виртуальная файловая система монтируется и рассматривается как локальный диск на этом узле. Вы можете подключать файловые системы, такие как служба файлов Azure, хранилище BLOB-объектов Azure, сетевая файловая система (NFS), включая [кэш Вфкст авере](../avere-vfxt/avere-vfxt-overview.md)или общую файловую систему Интернета (CIFS).

Из этой статьи вы узнаете, как подключить виртуальную файловую систему к пулу вычислений-узлов с помощью [библиотеки управления пакетной службой для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Подключение виртуальной файловой системы поддерживается для пулов пакетной службы, созданных в или после 2019-08-19. Пулы пакетной службы, созданные до 2019-08-19, не поддерживают эту функцию.
> 
> API-интерфейсы для монтирования файловых систем на кластерном узле являются частью библиотеки [.NET пакетной](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) службы.

## <a name="benefits-of-mounting-on-a-pool"></a>Преимущества подключения к пулу

Подключение файловой системы к пулу вместо того, чтобы позволить задачам получать собственные данные из большого набора данных, упрощает и повышает эффективность доступа к необходимым данным для задач.

Рассмотрим сценарий с несколькими задачами, которым требуется доступ к общему набору данных, например для отрисовки фильма. Каждая задача отображает один или несколько кадров за раз из файлов сцены. Подключение диска, содержащего файлы сцены, упрощает для вычислений узлов доступ к общим данным. Кроме того, базовая файловая система может быть выбрана и масштабироваться независимо от производительности и масштабируемости (пропускная способность и операции ввода-вывода), необходимой для количества параллельно обращающихся к данным узлов вычислений. Например, распределенный кэш в памяти [Авере вфкст](../avere-vfxt/avere-vfxt-overview.md) можно использовать для поддержки больших визуализаций движения изображений с тысячами параллельных узлов отрисовки, обращающихся к исходным данным, которые находятся в локальной среде. Кроме того, для данных, которые уже находятся в облачном хранилище BLOB-объектов, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) можно использовать для подключения этих данных в качестве локальной файловой системы. Blobfuse доступен только на узлах Linux, однако служба [файлов Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) предоставляет аналогичный рабочий процесс и доступна как в Windows, так и в Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Подключение виртуальной файловой системы к пулу  

Подключение виртуальной файловой системы к пулу делает файловую систему доступной каждому кластерному узлу в пуле. Файловая система настраивается либо при присоединении к пулу, либо при перезапуске или пересоздании образа узла.

Чтобы подключить файловую систему к пулу, создайте `MountConfiguration` объект. Выберите объект, соответствующий виртуальной файловой системе `AzureBlobFileSystemConfiguration`:, `AzureFileShareConfiguration`, `NfsMountConfiguration`или `CifsMountConfiguration`.

Все объекты конфигурации подключения должны иметь следующие базовые параметры. Некоторые конфигурации подключения имеют параметры, относящиеся к используемой файловой системе, которые более подробно обсуждаются в примерах кода.

- **Имя или источник учетной записи**: Чтобы подключить виртуальную общую папку, требуется имя учетной записи хранения или ее источник.
- **Относительный путь подключения или источник**: Расположение файловой системы, смонтированной на узле вычислений, относительно стандартного `fsmounts` каталога, доступного на узле с помощью. `AZ_BATCH_NODE_MOUNTS_DIR` Точное расположение зависит от операционной системы, используемой на узле. Например, физическое расположение на узле Ubuntu сопоставляется с `mnt\batch\tasks\fsmounts`, и на CentOS узле, которому `mnt\resources\batch\tasks\fsmounts`он сопоставлен.
- **Параметры подключения или параметры blobfuse**: Эти параметры описывают конкретные параметры для подключения файловой системы.

После создания `MountConfigurationList` объекта назначьте его свойству при создании пула. `MountConfiguration` Файловая система монтируется при присоединении узла к пулу или при перезапуске или пересоздании образа узла.

При подключении файловой системы создается переменная `AZ_BATCH_NODE_MOUNTS_DIR` среды, указывающая на расположение подключенных файловых систем, а также файлы журналов, которые полезны для устранения неполадок и отладки. Файлы журнала более подробно описаны в разделе [Диагностика ошибок подключения](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Максимальное число смонтированных файловых систем в пуле — 10. Дополнительные сведения и другие ограничения см. в разделе [квоты и ограничения пакетной службы](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Примеры

В следующих примерах кода демонстрируется подключение множества общих файловых ресурсов к пулу кластерных узлов.

### <a name="azure-files-share"></a>Файловый ресурс Azure

Служба "файлы Azure" является стандартным предложением облачной файловой системы Azure. Дополнительные сведения о том, как получить параметры в образце кода конфигурации подключения, см. в разделе [Использование общей папки службы файлов Azure](../storage/files/storage-how-to-use-files-windows.md).

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

### <a name="azure-blob-file-system"></a>Файловая система BLOB-объектов Azure

Другой вариант — использовать хранилище BLOB-объектов Azure через [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Для подключения файловой системы BLOB-объектов `AccountKey` требуется `SasKey` учетная запись хранения или. Сведения о получении этих ключей см. в разделах [Просмотр ключей учетной записи](../storage/common/storage-account-manage.md#view-account-keys-and-connection-string)или [использование подписанных URL-адрес (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Дополнительные сведения об использовании blobfuse см. в статье [часто задаваемые вопросы об устранении неполадок](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)blobfuse. Чтобы получить доступ по умолчанию к подключенному каталогу blobfuse, запустите задачу от имени **администратора**. Blobfuse подключает каталог в пространстве пользователя, и при создании пула он монтируется в качестве корневого. В Linux все задачи **администратора** являются корневыми. Все параметры для модуля ПРЕДОХРАНИТЕЛей описаны на [странице справочника](http://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)по предохранителю.

В дополнение к руководству по устранению неполадок, проблемы GitHub в репозитории blobfuse — это полезный способ проверки текущих проблем и решений blobfuse. Дополнительные сведения см. в разделе [проблемы blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

> [!NOTE]
> В настоящее время Blobfuse не поддерживается в Debian. Дополнительные сведения см. в разделе [Поддерживаемые номера SKU](#supported-skus) .

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

### <a name="network-file-system"></a>Сетевая файловая система

Сетевые файловые системы (NFS) также могут быть подключены к узлам пула, что позволяет узлам пакетной службы Azure легко получать доступ к традиционным файловым системам. Это может быть один сервер NFS, развернутый в облаке, или локальный сервер NFS, доступ к которому осуществляется через виртуальную сеть. Кроме того, воспользуйтесь преимуществами распределенного в памяти кэша [Авере вфкст](../avere-vfxt/avere-vfxt-overview.md) , который обеспечивает бесперебойное подключение к локальному хранилищу, считывание данных по запросу в свой кэш и обеспечивает высокую производительность и масштабируемость вычислений на основе облака. узлами.

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

### <a name="common-internet-file-system"></a>Общая файловая система Интернета

Стандартные файловые системы Интернета (CIFS) также могут быть подключены к узлам пула, что позволяет узлам пакетной службы Azure легко получать доступ к традиционным файловым системам. CIFS — это протокол общего доступа к файлам, обеспечивающий открытый и кросс-платформенный механизм для запроса файлов и служб сетевого сервера. CIFS основан на усовершенствованной версии протокола SMB для доступа к файлам в Интернете и интрасети и используется для подключения внешних файловых систем на узлах Windows. Дополнительные сведения о протоколе SMB см. в разделе [файловый сервер и SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

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

## <a name="diagnose-mount-errors"></a>Диагностика ошибок подключения

В случае сбоя конфигурации подключения кластерный узел в пуле завершится сбоем и состояние узла станет непригодным для использования. Чтобы диагностировать сбой конфигурации подключения, проверьте [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) свойство на наличие подробных сведений об ошибке.

Чтобы получить файлы журналов для отладки, используйте [OutputFiles](batch-task-output-files.md) для передачи `*.log` файлов. Файлы содержат сведения об монтировании файловой системы `AZ_BATCH_NODE_MOUNTS_DIR` в расположении. `*.log` Файлы журнала подключения имеют формат: `<type>-<mountDirOrDrive>.log` для каждого подключения. Например, `cifs` подключение в каталоге подключения с именем `test` будет иметь файл журнала подключения с именем: `cifs-test.log`.

## <a name="supported-skus"></a>Поддерживаемые номера SKU

| Издатель | Предложение | номер SKU | Файловый ресурс Azure | Blobfuse | Подключение NFS | Подключение CIFS |
|---|---|---|---|---|---|---|
| пакет (1) | rendering-centos73 | rendering | :heavy_check_mark: <br>Примечание. Совместимо с CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Канонический | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8, 9 | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Примечание. Совместимо с CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Примечание. Поддержка хранилища A_8 или 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | линуксдсвмубунту | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о подключении общего файлового ресурса Azure с [Windows](../storage/files/storage-how-to-use-files-windows.md) или [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Узнайте, как использовать и подключать виртуальные файловые системы [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Дополнительные сведения о NFS и ее приложениях см. в разделе [Обзор сетевой файловой системы](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) .
- Дополнительные сведения о CIFS см. в [статье Общие сведения о протоколе Microsoft SMB и протоколе CIFS](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .

---
title: Планирование развертывания службы файлов Azure | Документы Майкрософт
description: Узнайте, что необходимо учесть при планировании развертывания службы "Файлы Azure".
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227868"
---
# <a name="planning-for-an-azure-files-deployment"></a>Планирование развертывания службы файлов Azure

[Служба файлов Azure](storage-files-introduction.md) предлагает полностью управляемые общие файловые ресурсы в облаке, доступ к которым можно получить с помощью стандартного отраслевого протокола SMB. Так как служба файлов Azure является полностью управляемой, ее развертывание в рабочих сценариях гораздо проще развертывания файлового сервера или устройства NAS и управления им. В этой статье рассматриваются аспекты, которые следует учитывать при развертывании файлового ресурса Azure для использования в рабочей среде организации.

## <a name="management-concepts"></a>Основные принципы управления

 На схеме ниже показаны компоненты управления службой файлов Azure.

![Структура файла](./media/storage-files-introduction/files-concepts.png)

* **Учетная запись хранения**. Весь доступ к хранилищу Azure осуществляется с помощью учетной записи хранения. Сведения о емкости учетной записи хранения см. в статье [о целевых показателях масштабируемости и производительности службы хранилища Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Общая папка.** Общая папка хранилища файлов представляет собой общую папку с файлами в Azure, использующую протокол SMB. Все каталоги и файлы должны быть созданы в родительской общей папке. An account can contain an unlimited number of shares and a share can store an unlimited number of files, up to the total capacity of the file share. The total capacity for premium and standard file shares is 100 TiB.

* **Каталог.** Необязательная иерархия каталогов.

* **Файл.** Файл в совместно используемом ресурсе. Файлы могут иметь размер до 1 ТБ.

* **Формат URL-адреса.** В запросах к файловому ресурсу Azure, выполняемых с помощью протокола REST, к файлам можно обращаться, используя URL-адрес в следующем формате:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Метод доступа к данным

Служба файлов Azure предлагает два встроенных удобных метода доступа к данным, которые можно использовать по отдельности или в сочетании друг с другом.

1. **Прямой доступ к облаку**. Файловый ресурс Azure можно подключить в ОС [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) или [Linux](storage-how-to-use-files-linux.md) через SMB или REST API службы файлов. При использовании SMB операции чтения и записи выполняются непосредственно в файловом ресурсе в Azure. Для подключения к виртуальной машине в Azure клиент SMB в операционной системе должен поддерживать по меньшей мере протокол SMB 2.1. Для локального подключения, например к рабочей станции пользователя, клиент SMB на рабочей станции должен поддерживать по меньшей мере протокол SMB 3.0 (с шифрованием). Кроме SMB, новые приложения или службы могут напрямую обращаться к файловому ресурсу посредством File REST, который является простым и масштабируемым API-интерфейсом для разработки программного обеспечения.
2. **Служба синхронизации Azure файлов**. С помощью службы синхронизации файлов Azure можно реплицировать файловые ресурсы на локальные серверы Windows Server или в Azure. Ваши пользователи могут получить доступ к файловому ресурсу через Windows Server, например через общий ресурс SMB или NFS. Это полезно в сценариях, когда доступ к данным и их изменения выполняются далеко за пределами центров обработки данных Azure, например как в сценарии с филиалом. Данные можно реплицировать между несколькими конечными точками Windows Server, например между несколькими филиалами. Наконец, данные могут быть размещены на нескольких уровнях в службе файлов Azure так, что они по-прежнему будут доступны через сервер, однако на сервере отсутствует полная копия данных. Данные можно легко извлечь при открытии пользователем.

В следующей таблице показано, как пользователи и приложения могут обращаться к файловому ресурсу Azure.

| | Прямой доступ к облаку | Синхронизация файлов Azure |
|------------------------|------------|-----------------|
| Какие протоколы необходимо использовать? | Служба файлов Azure поддерживает SMB 2.1, SMB 3.0 и File REST API. | Доступ к файловому ресурсу Azure с помощью любого поддерживаемого протокола в Windows Server (SMB, NFS, FTPS и т. д.) |  
| Где выполняется рабочая нагрузка? | **В Azure**. Служба файлов Azure обеспечивает прямой доступ к данным. | **В локальной среде с медленным сетевым подключением**. Клиенты Windows, Linux и macOS могут подключить локальный файловый ресурс Windows в качестве быстрого кэша файлового ресурса Azure. |
| Какой уровень списков ACL требуется? | Уровень общего доступа и уровень файла. | Уровень общего доступа, уровень файла и уровень пользователя. |

## <a name="data-security"></a>Безопасность данных

Служба файлов Azure предлагает несколько встроенных возможностей для обеспечения безопасности данных.

* Поддержка шифрования для протоколов сетевой передачи: шифрование SMB 3.0 и File REST по HTTPS. По умолчанию: 
    * Clients that support SMB 3.0 encryption send and receive data over an encrypted channel.
    * Clients that do not support SMB 3.0 with encryption can communicate intra-datacenter over SMB 2.1 or SMB 3.0 without encryption. Клиенты SMB не могут взаимодействовать между ЦОД по протоколам SMB 2.1 или SMB 3.0 без шифрования.
    * Клиенты могут взаимодействовать через File REST по протоколу HTTP или HTTPS.
* Шифрование неактивных данных ([шифрование Службы хранилища Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)). Шифрование службы хранилища (SSE) включено во всех учетных записях хранения. Неактивные данные шифруются с помощью полностью управляемых ключей. Шифрование неактивных данных не увеличивает затраты на хранение и не снижает производительность. 
* Optional requirement of encrypted data in-transit: when selected, Azure Files rejects access to the data over unencrypted channels. В частности, будут разрешены только подключения по протоколам HTTPS и SMB 3.0 с шифрованием.

    > [!Important]  
    > Необходимость безопасной передачи данных приведет к сбою старых клиентов SMB, не поддерживающих взаимодействие с SMB 3.0 с шифрованием. Дополнительные сведения см. в статьях [Использование общей папки Azure в Windows](storage-how-to-use-files-windows.md), [Использование файлов Azure в Linux](storage-how-to-use-files-linux.md), [Подключение общей папки Azure через протокол SMB в macOS](storage-how-to-use-files-mac.md).

Для обеспечения максимальной безопасности настоятельно рекомендуется всегда включать шифрование данных при хранении и шифрование данных во время передачи при каждом использовании современных клиентов для доступа к данным. Например, если необходимо подключить общую папку в виртуальной машине с Windows Server 2008 R2, которая поддерживает только SMB 2.1, необходимо разрешить передачу незашифрованного трафика в хранилище, так как SMB 2.1 не поддерживает шифрование.

Если для доступа к файловому ресурсу Azure вы используете службу "Синхронизация файлов Azure", для синхронизации данных с серверами Windows Server всегда будут использоваться протоколы HTTPS и SMB 3.0 с шифрованием (независимо от того, нужно ли вам шифровать неактивные данные).

## <a name="file-share-performance-tiers"></a>Уровни производительности файлового ресурса

Azure Files offers two performance tiers: standard and premium.

### <a name="standard-file-shares"></a>Общие файлы уровня "Стандарт"

Standard file shares are backed by hard disk drives (HDDs). Standard file shares provide reliable performance for IO workloads that are less sensitive to performance variability such as general-purpose file shares and dev/test environments. Файловые ресурсы уровня "Стандартный" доступны только в модели выставления счетов с оплатой по мере использования.

> [!IMPORTANT]
> If you want to use file shares larger than 5 TiB, see the [Onboard to larger file shares (standard tier)](#onboard-to-larger-file-shares-standard-tier) section for steps to onboard, as well as regional availability and restrictions.

### <a name="premium-file-shares"></a>Premium file shares

Premium file shares are backed by solid-state drives (SSDs). Premium file shares provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. This makes them suitable for a wide variety of workloads like databases, web site hosting, and development environments. Файловые ресурсы уровня "Премиум" доступны только в подготовленной модели выставления счетов. Premium file shares use a deployment model separate from standard file shares.

Azure Backup is available for premium file shares and Azure Kubernetes Service supports premium file shares in version 1.13 and above.

If you'd like to learn how to create a premium file share, see our article on the subject: [How to create an Azure premium file storage account](storage-how-to-create-premium-fileshare.md).

Currently, you cannot directly convert between a standard file share and a premium file share. If you would like to switch to either tier, you must create a new file share in that tier and manually copy the data from your original share to the new share you created. You can do this using any of the Azure Files supported copy tools, such as Robocopy or AzCopy.

> [!IMPORTANT]
> Premium file shares are available with LRS in most regions that offer storage accounts and with ZRS in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the [products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) page for Azure. To find out what regions support ZRS, see [Support coverage and regional availability](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> To help us prioritize new regions and premium tier features, please fill out this [survey](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Подготовленные ресурсы

Файловые ресурсы уровня "Премиум" предоставляются на основе зафиксированной пропорции ГиБ/операции ввода-вывода в секунду/пропускная способность. Для каждого подготовленного ГиБ ресурс будет выдаваться на одну пропускную способность операций ввода-вывода в секунду и 0,1 МиБ/с пропускной способности в условиях максимальной нагрузки на ресурс. Минимально допустимый уровень подготовки составляет 100 ГиБ с минимальным числом операций ввода-вывода в секунду и пропускной способностью.

Наилучшим возможным образом все ресурсы могут ограничиваться до трех операций ввода-вывода в секунду на ГиБ подготовленного хранилища в течение 60 минут или дольше в зависимости от размера ресурса. Новые ресурсы начинаются с полного пакетного кредита на основе подготовленной мощности.

Shares must be provisioned in 1 GiB increments. Minimum size is 100 GiB, next size is 101 GiB and so on.

> [!TIP]
> Baseline IOPS = 1 * provisioned GiB. (Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Up to a max of 100,000 IOPS).
>
> egress rate = 60 MiB/s + 0.06 * provisioned GiB
>
> ingress rate = 40 MiB/s + 0.04 * provisioned GiB

Provisioned share size is specified by share quota. Share quota can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/Throughput scale changes will be effective within a few minutes after the size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

The following table illustrates a few examples of these formulae for the provisioned share sizes:

|Capacity (GiB) | Базовые показатели операций ввода-вывода в секунду | Burst IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 300     | 66   | 44   |
|500         | 500     | Up to 1,500   | 90   | 60   |
|1024       | 1024   | Up to 3,072   | 122   | 81   |
|5,120       | 5,120   | Up to 15,360  | 368   | 245   |
|10,240      | 10,240  | Up to 30,720  | 675 | 450   |
|33,792      | 33,792  | Up to 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | Up to 100,000 | 3132 | 2,088   |
|102,400     | 100 000 | Up to 100,000 | 6,204 | 4,136   |

> [!NOTE]
> File shares performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. To achieve maximum performance scale, spread the load across multiple VMs. Please refer [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

#### <a name="bursting"></a>Bursting

Premium file shares can burst their IOPS up to a factor of three. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee, file shares can burst *up to* the limit.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 100 baseline IOPS. If actual traffic on the share was 40 IOPS for a specific 1-second interval, then the 60 unused IOPS are credited to a burst bucket. These credits will then be used later when operations would exceed the baseline IOPs.

> [!TIP]
> Size of the burst bucket = Baseline IOPS * 2 * 3600.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst. Shares can continue to burst as long as credits are remaining, though shares smaller than 50 TiB will only stay at the burst limit for up to an hour. Shares larger than 50 TiB can technically exceed this one hour limit, up to two hours but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is bursting.
- Remaining constant, when there are either no credits or baseline IOPS are in use.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## <a name="file-share-redundancy"></a>Избыточность файловых ресурсов

Azure Files standard shares supports four data redundancy options: locally redundant storage (LRS), zone redundant storage (ZRS), geo-redundant storage (GRS), and geo-zone-redundant storage (GZRS) (preview).

Azure Files premium shares support both LRS and ZRS, ZRS is currently available in a smaller subset of regions.

В следующих разделах описаны различия между разными вариантами обеспечения избыточности.

### <a name="locally-redundant-storage"></a>Локально избыточное хранилище

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Хранилище, избыточное в пределах зоны

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Гео-избыточное хранилище

> [!Warning]  
> Если вы используете общую папку Azure в качестве конечной точки облака в учетной записи хранения GRS, нет необходимости выполнять отработку отказа учетной записи хранения. Это приведет к прекращению синхронизации или даже к непредвиденной потере данных, если некоторые файлы недавно стали многоуровневыми. В случае потери региона Azure корпорация Майкрософт активирует отработку отказа учетной записи хранения способом, который совместим со службой "Синхронизация файлов Azure".

Геоизбыточное хранилище (GRS) предназначено для обеспечения устойчивости объектов на уровне 99,99999999999999 % (16 девяток) в течение определенного года за счет репликации данных в дополнительный регион, который находится в сотнях километров от основного региона. Если в учетной записи хранения включена репликация GRS, данные будут устойчивы даже в случае отключения электричества во всем регионе или аварии, при которой первичный регион не может быть восстановлен.

If you opt for read-access geo-redundant storage (RA-GRS), you should know that Azure File does not support read-access geo-redundant storage (RA-GRS) in any region at this time. File shares in the RA-GRS storage account work like they would in GRS accounts and are charged GRS prices.

GRS реплицирует данные в другой центр обработки данных в дополнительном регионе, но эти данные будут доступны для чтения, только если корпорация Майкрософт инициирует отработку отказа из основного региона в дополнительный.

For a storage account with GRS enabled, all data is first replicated with locally redundant storage (LRS). Обновление сначала фиксируется в основное расположение и реплицируется с использованием локально избыточного хранилища. Затем обновление реплицируется асинхронно в дополнительный регион, используя геоизбыточное хранилище. При записи данных в дополнительное расположение они также реплицируются в этом расположении с использованием LRS.

Основной и дополнительный регионы управляют репликами в отдельных доменах сбоя и доменах обновления в рамках единицы масштабирования хранилища. Единица масштабирования хранилища — это основная единица репликации в центре обработки данных. Репликация на этом уровне предоставляется с помощью LRS. Дополнительные сведения см. в статье [Локально избыточное хранилище (LRS). Недорогая избыточность данных для службы хранилища Azure](../common/storage-redundancy-lrs.md).

При выборе варианта репликации учитывайте следующие моменты.

* Geo-zone-redundant storage (GZRS) (preview) provides high availability together with maximum durability by replicating data synchronously across three Azure availability zones and then replicating data asynchronously to the secondary region. You can also enable read access to the secondary region. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year. For more information on GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](../common/storage-redundancy-gzrs.md).
* Zone-redundant storage (ZRS) provides highly availability with synchronous replication and may be a better choice for some scenarios than GRS. Дополнительные сведения о ZRS см. [здесь](../common/storage-redundancy-zrs.md).
* Асинхронная репликация включает в себя задержку между временем записи данных в основном регионе и временем, когда данные реплицируются в дополнительный регион. В случае региональной аварии изменения, которые еще не реплицированные в дополнительный регион, могут быть потеряны, если данные будет невозможно восстановить из основного региона.
* С геоизбыточным хранилищем реплика будет недоступна для чтения или записи, пока корпорация Майкрософт не инициирует отработку отказа в дополнительном регионе. В случае отработки отказа, доступ на чтение и запись будет получен после ее завершения. Дополнительные сведения см. в статье [Что делать в случае простоя службы хранилища Azure](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboard to larger file shares (standard tier)

This section only applies to the standard file shares. All premium file shares are available with 100 TiB capacity.

### <a name="restrictions"></a>Ограничения

- LRS/ZRS to GRS/GZRS account conversion will not be possible for any storage account with large file shares enabled.

### <a name="regional-availability"></a>Доступность по регионам

Standard file shares are available in all regions up to 5 TiB. In certain regions, they are available with a 100 TiB limit, those regions are listed in the following table:

|Регион |Supported redundancy |
|-------|---------|
|Восточная Австралия |LRS     |
|Юго-Восточная Австралия|LRS |
|Центральная Канада  |LRS     |
|Восточная Канада     |LRS     |
|Центральная Индия  |LRS     |
|Central US*   |LRS     |
|Восточная Азия      |LRS     |
|East US*        |LRS     |
|East US 2*      |LRS     |
|Центральная Франция |LRS, ZRS|
|Южная Франция   |LRS     |
|Северная Европа   |LRS     |
|Южная Индия    |LRS     |
|Юго-Восточная Азия |LRS, ZRS|
|Центрально-западная часть США|LRS     |
|West Europe*    |LRS, ZRS|
|West US*        |LRS     |
|Западная часть США 2      |LRS, ZRS|

\* Supported for new accounts, not all existing accounts have completed the upgrade process. You can check if your existing storage accounts have completed the upgrade process by attempting to [Enable large file shares](storage-files-how-to-create-large-file-share.md).

To help us prioritize new regions and features, please fill out this [survey](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Enable and create larger file shares

To begin using larger file shares, see our article [How to enable and create large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Модель роста данных

Today, the maximum size for an Azure file share is 100 TiB. Из-за этого ограничения при развертывании файлового ресурса Azure следует учитывать ожидаемый рост объема данных.

It is possible to sync multiple Azure file shares to a single Windows File Server with Azure File Sync. This allows you to ensure that older, large file shares that you may have on-premises can be brought into Azure File Sync. For more information, see [Planning for an Azure File Sync Deployment](storage-files-planning.md).

## <a name="data-transfer-method"></a>Метод передачи данных

Существует множество простых вариантов массовой передачи данных из существующего файлового ресурса, такого как файловый ресурс в локальной среде, в службу файлов Azure. В число распространенных методов входят следующие (список не является исчерпывающим).

* **Служба "Синхронизация файлов Azure"** . Во время первой синхронизации между файловым ресурсом Azure (облачная конечная точка) и пространством имен каталогов Windows (серверная конечная точка) служба "Синхронизация файлов Azure" реплицирует все данные из существующего файлового ресурса в службу файлов Azure.
* **[Служба "Импорт и экспорт Azure"](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** . Эта служба позволяет безопасно переносить большие объемы данных в файловый ресурс Azure, отправляя их на жестких дисках в центр обработки данных Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** . Это хорошо известное средство копирования, которое входит в состав Windows и Windows Server. Robocopy можно применять для передачи данных в службу файлов Azure путем локального подключения файлового ресурса и последующего использования подключенного расположения в качестве назначения в команде Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** — это служебная программа командной строки. Она предназначена для копирования данных из службы файлов Azure, хранилища BLOB-объектов Azure и обратно с помощью простых команд, обеспечивающих оптимальную производительность.

## <a name="next-steps"></a>Дальнейшие действия
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Планирование развертывания службы синхронизации файлов Azure)
* [How to deploy Azure Files](storage-files-deployment-guide.md) (Развертывание службы "Файлы Azure")
* [Развертывание службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-deployment-guide.md)

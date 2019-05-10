---
title: Восстановление резервной копии в Azure Service Fabric | Документация Майкрософт
description: Использование функций периодического резервного копирования и восстановления в Service Fabric для восстановления данных приложения из резервной копии.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: e4ada412547360f97e869d3312b65d869fa3df48
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413718"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Восстановление резервной копии в Azure Service Fabric

В Azure Service Fabric надежные службы с отслеживанием состояния и Reliable Actors могут поддерживать заслуживающее доверия изменяемое состояние после выполнения запроса и получения ответной транзакции. Служба с отслеживанием состояния может стать неработоспособной в течение длительного времени или привести к потере данных из-за сбоя. В этом случае ее нужно восстановить из последней приемлемой резервной копии, чтобы она могла продолжить работу.

Например, можно настроить службу для резервного копирования данных, чтобы защитить ее от следующих сценариев.

- **Аварийное восстановление**. Полная потеря всего кластера Service Fabric.
- **Потеря данных**. Полная потеря большей части реплик для служебной секции
- **Потеря данных**. Случайное удаление или повреждение службы. Например, администратор ошибочно удаляет службу.
- **Повреждение данных**. Повреждение данных может произойти из-за ошибок в службе. Например, повреждение данных может произойти, когда после обновления кода служба записывает содержащие ошибки данные в надежную коллекцию. В этом случае может потребоваться восстановить код и данные до предыдущего состояния.

## <a name="prerequisites"></a>Технические условия

- Чтобы активировать восстановление, для кластера должна быть включена _служба анализа сбоев (FAS)_.
- _Служба резервного копирования и восстановления (BRS)_ создает резервную копию.
- Восстановление можно активировать только в секции.
- Установите модуль Microsoft.ServiceFabric.Powershell.Http [Preview] для выполнения вызовов конфигурации.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Убедитесь, что кластер подключен с помощью `Connect-SFCluster` команду перед выполнением любой запрос конфигурации, с помощью модуля Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Активация восстановления

Восстановление может быть активировано в любом из следующих сценариев:

- восстановление данных в случае _аварийного восстановления_;
- восстановление данных в случае _повреждения или потери данных_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Восстановление данных в случае аварийного восстановления

В случае потери всего кластера Service Fabric данные секций надежной службы с отслеживанием состояния и Reliable Actors можно восстановить. Необходимую резервную копию можно выбрать из списка при использовании [GetBackupAPI с указанием сведений о хранилище резервных копий](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Можно использовать перечисление резервных копий для отдельного приложения, службы или секции.

В следующем примере предполагается, что потерян кластер, упомянутый в разделе [Включение периодического резервного копирования для надежной службы с отслеживанием состояния и Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). В этом случае `SampleApp` развертывается с включенной политикой резервного копирования, а для хранения резервных копий используется служба хранилища Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

Выполните сценарий PowerShell, чтобы вызвать REST API для получения списка резервных копий, созданных для всех секций внутри приложения `SampleApp`. Для вывода списка доступных резервных копий API требуются сведения о хранилище резервных копий.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Пример выходных данных описанного выше процесса.

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Чтобы активировать восстановление, выберите одну из резервных копий. Например, для текущего аварийного восстановления можно выбрать приведенную ниже резервную копию.

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Для API восстановления необходимо предоставить значения _BackupId_ и _BackupLocation_.

Кроме того, необходимо выбрать секцию назначения в альтернативном кластере, указанную в [схеме секционирования](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Резервная копия данных восстанавливается в секцию в альтернативном кластере, указанную в схеме секционирования из исходного потерянного кластера.

Если в альтернативном кластере используется идентификатор секции `1c42c47f-439e-4e09-98b9-88b8f60800c6`, его можно сопоставить с идентификатором секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22` в исходном кластере. Для этого можно использовать сравнение верхнего и нижнего ключей для _секционирования по диапазонам (UniformInt64Partition)_.

При _секционировании по именам_ для определения целевой секции в альтернативном кластере сравниваются значения имен.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

Запросить восстановление резервной копии секции кластера можно с помощью следующих [API восстановления](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Можно отслеживать ход восстановления с помощью TrackRestoreProgress.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Восстановление данных в случае _повреждения или потери данных_/__

В случае _потери_ или _повреждения_ данных можно восстановить секции надежной службы с отслеживанием состояния и Reliable Actors из любой выбранной резервной копии (если они были созданы).

Описанный ниже случай — это продолжение сценария в разделе [Включение периодического резервного копирования для надежной службы с отслеживанием состояния и Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). В этом примере для секции включена политика резервного копирования, и служба с требуемой частотой создает резервные копии в службе хранилища Azure.

Выберите резервную копию из выходных данных [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). В этом сценарии резервная копия в прошлом была создана из того же кластера.

Чтобы активировать восстановление, выберите резервную копию из списка. Для текущего случая _потери или повреждения данных_ выберите указанную ниже резервную копию.

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Для API восстановления укажите значения _BackupId_ и _BackupLocation_. Для кластера включено резервное копирование, поэтому _служба восстановления резервных копий_ Service Fabric вычисляет правильное расположение хранилища на основе соответствующей политики резервного копирования.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Можно отслеживать ход восстановления с помощью TrackRestoreProgress.

## <a name="track-restore-progress"></a>Отслеживание хода восстановления

Секция надежной службы с отслеживанием состояния или Reliable Actor в один момент времени может принять только один запрос на восстановление. Секция примет другой запрос только после завершения текущего запроса на восстановление. Но можно выполнять несколько запросов на восстановление одновременно в разных секциях.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Запрос на восстановление обрабатывается в следующем порядке состояний.

1. **Accepted**. Состояние восстановления _Accepted_ указывает, что активировано восстановление секции с правильным набором параметров.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**. Состояние восстановления _InProgress_ указывает, что выполняется восстановление данных в секции на основе резервной копии, указанной в запросе. Для секции в этот период возвращается состояние _dataloss_.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Success**, **Failure** или **Timeout**. Любое из этих состояний означает завершение запрошенного восстановления. Ниже описано значение каждого из этих состояний и подробное описание ответов для них.
    - **Выполнено**. Состояние восстановления _Success_ указывает на то, что состояние секции восстановлено. Секция возвращает состояния _RestoredEpoch_ и _RestoredLSN_, а также время в формате UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Сбой**. Состояние восстановления _Failure_ указывает, что запрос на восстановление завершился ошибкой. Сообщается причина ошибки.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**. Состояние восстановление _Timeout_ указывает, что для запроса превышено время ожидания. Создайте новый запрос на восстановление с большим значением [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Время ожидания по умолчанию составляет 10 минут. Убедитесь, что раздел вышел из состояния dataloss, прежде чем повторять запрос на восстановление.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Автоматическое восстановление

Для секций надежной службы с отслеживанием состояния и Reliable Actors в кластере Service Fabric можно настроить _автоматическое восстановление_. В политике резервного копирования для параметра `AutoRestore` задайте значение _true_. Если для секции включено _автоматическое восстановление_, то при обнаружении потери данных ее данные автоматически восстанавливаются из последней резервной копии. Дополнительные сведения можно найти в разделе 

- [Включение автоматического восстановления в политике резервного копирования](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Справочник по RestorePartition API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Справочник по GetPartitionRestoreProgress API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Дальнейшие действия
- [Основные сведения о настройке периодического резервного копирования](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restore REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore) (Справочник по REST API службы резервного копирования и восстановления)

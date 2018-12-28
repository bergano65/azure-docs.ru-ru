---
title: Восстановление резервной копии в Azure Service Fabric | Документация Майкрософт
description: Использование функции периодического резервного копирования и восстановления Service Fabric для восстановления данных приложения из резервной копии.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730724"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Восстановление резервной копии в Azure Service Fabric


Надежные службы с отслеживанием состояния и Reliable Actors поддерживают заслуживающее доверия изменяемое состояние даже за пределами пары "запрос — ответ" или полной транзакции. Если служба с отслеживанием состояния отключена в течение длительного времени или теряет информацию вследствие аварии, может возникнуть необходимость восстановления до последней приемлемой резервной копии состояния, чтобы продолжить работу службы.

Например, службе может потребоваться создать резервную копию своих данных для защиты от таких сценариев:

- В случае полной потери всего кластера Service Fabric **(аварийное восстановление)**.
- Полная потеря большей части реплик для служебной секции **(потеря данных)**.
- Если из-за административных ошибок произошло случайное удаление или повреждение состояния. Например, администратор с достаточными привилегиями ошибочно удалил службу **(потеря данных)**.
- Если из-за ошибок в службе происходит повреждение данных. Например, повреждения данных возникают, когда после обновления кода служба начинает запись содержащих ошибки данных в надежную коллекцию. В этом случае может потребоваться вернуть код и данные в предыдущее состояние **(повреждение данных)**.


## <a name="prerequisites"></a>Предварительные требования
* Чтобы запустить восстановление, для кластера должна быть включена _служба анализа сбоев_.
* Восстанавливаемая резервная копия должна быть создана с помощью _службы восстановления резервных копий_.
* Восстановление можно активировать только в секции.

## <a name="triggering-restore"></a>Активация восстановления

Восстановление можно выполнять в любом из следующих сценариев: 
* восстановление данных в случае _аварийного восстановления_;
* восстановление данных в случае _повреждения или потери данных_.



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Восстановление данных в случае _аварийного восстановления_
В случае потери всего кластера Service Fabric данные для секции надежной службы с отслеживанием состояния и Reliable Actors можно восстановить в другой кластер. Подходящую резервную копию можно выбрать в перечислении в руководстве по [получению резервных копий из хранилища резервных копий](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Перечисление резервных копий можно использовать для отдельного приложения, службы или секции.

Для примера предположим, что был потерян тот же кластер, который обсуждался в руководстве по [включению периодического резервного копирования для службы с отслеживанием состояния и Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Для него было выполнено развертывание `SampleApp`, где для секции была включена политика резервного копирования и создавались резервные копии в службе хранилища Azure. 


Выполните следующий скрипт PowerShell, чтобы вызвать REST API для перечисления резервных копий, созданных для всех секций в приложении `SampleApp`, размещенном в потерянном кластере Service Fabric. API перечисления запрашивает обязательные сведения о хранилище, в котором хранятся резервные копии приложения, чтобы перечислить доступные резервные копии. 

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



Для запуска восстановления нам нужно выбрать подходящую резервную копию. Предположим, что для нашего аварийного восстановления подходит следующая резервная копия

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

Для API восстановления необходимо предоставить значения __BackupId__ и __BackupLocation__. Также нужно выбрать раздел в альтернативном кластере, который указан в [схеме секционирования](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Пользователь должн правильно указать для восстановления резервной копии целевую секцию, расположенную в альтернативном кластере согласно схеме секционирования в исходном (потерянном) кластере.

Предположим, в альтернативном кластере используется идентификатор секции `1c42c47f-439e-4e09-98b9-88b8f60800c6`, которому соответствует идентификатор секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22` в исходном кластере. Это сопоставление отслеживается путем сравнения верхнего и нижнего ключей для _секционирования по диапазонам (UniformInt64Partition)_.

При _секционировании по именам_ для определения целевой секции в альтернативном кластере сравниваются значения имен.

Восстановление запускается для секции в кластере резервного восстановления через [API восстановления](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition).

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
Ход восстановления можно отслеживать с помощью [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Восстановление данных в случае _повреждения или потери данных_

В случае _потери_ или _повреждения_ данные можно восстановить для секций надежной службы с отслеживанием состояния и Reliable Actors из любой выбранной резервной копии. Следующий пример является продолжением примера, который уже упоминался в руководстве по [включению периодического резервного копирования для надежной службы с отслеживанием состояния и Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), в котором для секций включена политика резервного копирования и резервное копирование выполняется в службе хранилища Azure с требуемой частотой. 

Нужная резервная копия выбирается из выходных данных [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). В этом сценарии резервная копия в прошлом была создана из того же кластера.
Для запуска восстановления нам нужно выбрать из списка подходящую резервную копию. Для нашего примера с _потерей данных_ / _повреждением данных_ давайте считать нужной резервной копией вот эту:

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

Для API восстановления необходимо предоставить значения __BackupId__ и __BackupLocation__. Так как для кластера включено резервное копирование, _служба восстановления резервных копий_ Service Fabric вычисляет правильное расположение хранилища на основе соответствующей политики резервного копирования.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Ход восстановления можно отслеживать с помощью [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).


## <a name="tracking-restore-progress"></a>Отслеживание хода восстановления

Секция надежной службы с отслеживанием состояния или Reliable Actor в один момент времени может принять только один запрос на восстановление. Следующий запрос будет принят только после выполнения текущего запроса на восстановление. Но можно выполнять несколько запросов на восстановление одновременно в разных секциях.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

Запрос на восстановление обрабатывается в следующем порядке состояний:

1. __Accepted__ — состояние восстановления _Accepted_ указывает, что получен допустимый запрос с правильным набором параметров.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ — состояние восстановления _InProgress_ указывает, что для секции применяется восстановление на основе резервной копии, указанной в запросе. Для секции в этот период возвращается состояние _dataloss_.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Success__/ __Failure__/ __Timeout__ — любое из этих состояний означает завершение запрошенного восстановления. Ниже описаны значение каждого из этих состояний и подробностей ответов для них.
       
    * __Success__ — состояние восстановления _Success_ указывает, что состояние секции полностью восстановлено. Ответ в этом случае возвращает параметры RestoreEpoch и RestordLSN для секции, а также время в формате UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Failure__ — состояние восстановления _Failure_ указывает, что запрос на восстановление завершился ошибкой. Причины сбоя будут указаны в ответе на запрос.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Timeout__ — состояние восстановление _Timeout_ указывает, что для запроса превышено время ожидания. В таких случаях мы рекомендуем выполнять новый запрос на восстановление с более высоким значением [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). По умолчанию используется время ожидания 10 минут. Мы рекомендуем сначала убедиться, что раздел вышел из состояния data loss, прежде чем возобновлять попытку восстановления.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Автоматическое восстановление

Для секций надежной службы с отслеживанием состояния и Reliable Actors в кластере Service Fabric можно настроить _автоматическое восстановление_. При создании политики резервного копирования в ней можно указать для параметра `AutoRestore` значение _true_.  Если для секции включено _автоматическое восстановление_, при обнаружении потери данных ее данные восстанавливаются из последней резервной копии.
 
 [Включение автоматического восстановления в политике резервного копирования](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Справочник по API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[Справочник по API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Дополнительная информация
- [Основные сведения о настройке периодического резервного копирования](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restore REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore) (Справочник по REST API службы резервного копирования и восстановления)

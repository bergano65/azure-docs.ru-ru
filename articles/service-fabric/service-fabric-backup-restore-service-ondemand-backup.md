---
title: Резервное копирование по запросу в Azure Service Fabric | Документация Майкрософт
description: Использование функции резервного копирования и восстановления в Service Fabric для резервного копирования данных приложения по мере необходимости.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: bed3402de83984cae9134fe44058980ec18861b3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413942"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Резервное копирование по запросу в Azure Service Fabric

Можно создать резервную копию данных Reliable Services с отслеживанием состояния и данных Reliable Actors на случай аварии или потери данных.

Azure Service Fabric предоставляет функции для [периодического резервного копирования данных](service-fabric-backuprestoreservice-quickstart-azurecluster.md) и резервного копирования данных по мере необходимости. Резервное копирование по запросу предотвращает _потерю_/_повреждение данных_ из-за плановых изменений в основной службе или ее среде.

Функции резервного копирования по запросу позволяют записать сведения о состоянии служб перед любой выполняющейся вручную операцией, которая относится к этой службе или к ее среде. Например, когда вы вносите изменения в двоичные файлы службы при ее обновлении или переходе на более раннюю версию. В этом случае резервное копирование по запросу защищает данные от повреждения из-за ошибок в коде приложения.
## <a name="prerequisites"></a>Технические условия

- Установите модуль Microsoft.ServiceFabric.Powershell.Http [Preview] для выполнения вызовов конфигурации.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Убедитесь, что кластер подключен с помощью `Connect-SFCluster` команду перед выполнением любой запрос конфигурации, с помощью модуля Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Запуск резервного копирования по запросу

Для резервного копирования по запросу требуются сведения о хранилище, чтобы отправлять файлы резервных копий. Расположение хранилища для резервного копирования данных можно указать в политике периодического резервного копирования или в запросе на резервное копирование по запросу.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Резервное копирование по запросу в хранилище, указанное в политике периодического резервного копирования

Можно настроить политику периодического резервного копирования таким образом, чтобы использовалась секция службы Reliable Services с отслеживанием состояния или Reliable Actors для дополнительного резервного копирования по запросу в хранилище.

Описанный ниже случай — это продолжение сценария в разделе о [включении периодического резервного копирования для службы Reliable Services с отслеживанием состояния и Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). В этом случае вы настраиваете политику резервного копирования для использования секции, и резервное копирование выполняется в Службе хранилища Azure с установленной частотой.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

Используйте API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition), чтобы инициировать резервное копирование по запросу для идентификатора секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Используйте API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress), чтобы включить отслеживание для [хода резервного копирования по запросу](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Резервное копирование по запросу в указанное хранилище

Вы можете создать запрос на резервное копирование для секции службы Reliable Services с отслеживанием состояния или Reliable Actors. Предоставьте сведения о хранилище как часть запроса на резервное копирование по запросу.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

Используйте API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition), чтобы инициировать резервное копирование по запросу для идентификатора секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Включите следующие сведения о Службе хранилища Azure:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Можно настроить отслеживание для [хода резервного копирования по запросу](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) с помощью API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).

## <a name="tracking-on-demand-backup-progress"></a>Отслеживание хода выполнения резервного копирования по запросу

Секция Reliable Services с отслеживанием состояния или Reliable Actors может единовременно принять только один запрос на резервное копирование по запросу. Следующий запрос будет приниматься только после выполнения текущего.

Можно одновременно выполнять несколько запросов на резервное копирование по запросу в разных секциях.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>С помощью Microsoft.ServiceFabric.Powershell.Http модуля PowerShell

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Вызов REST, с помощью Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Возможные состояния запросов на резервное копирование по запросу:

- **Accepted** (Принято). Резервное копирование инициировано в секции и выполняется.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Success** (Успешно), **Failure** (Сбой) или **Timeout** (Превышено время ожидания). Любое из этих состояний означает завершение запрошенного копирования.
  - **Выполнено**. Состояние резервного копирования _Success_ (Успешно) указывает на успешное создание резервной копии состояния секции. В ответе в этом случае возвращаются параметры _BackupEpoch_ и _BackupLSN_ для секции, а также время в формате UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Сбой**. Состояние резервной копии _Failure_ (Сбой) указывает на то, что при резервном копировании состояния секции произошел сбой. Причины сбоя указаны в ответе на запрос.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout** (Превышено время ожидания). Состояние резервного копирования _Timeout_ (Превышено время ожидания) означает, что в течение заданного промежутка времени не удалось выполнить резервное копирование состояния секции. Время ожидания по умолчанию составляет 10 минут. В этом случае инициируйте новый запрос на резервное копирование с увеличенным значением [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout).
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Основные сведения о настройке периодического резервного копирования](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Справочник по REST API BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

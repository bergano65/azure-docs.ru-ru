---
title: Резервное копирование по запросу в Azure Service Fabric | Документация Майкрософт
description: Использование функции резервного копирования и восстановления Service Fabric для резервного копирования данных приложения по мере необходимости.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730707"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Резервное копирование по запросу в Azure Service Fabric

Можно создать резервную копию данных Reliable Services с отслеживанием состояния и данных Reliable Actors на случай аварии или потери данных.

Service Fabric содержит функции для [периодического резервного копирования данных](service-fabric-backuprestoreservice-quickstart-azurecluster.md) и резервного копирования данных по мере необходимости. Резервное копирование по запросу предотвращает _потерю данных_ / _повреждение данных_, которые возникают из-за плановых изменений в основной службе или ее среде.

Функции резервного копирования по запросу позволяют записать сведения о состоянии служб перед любой выполняющейся вручную операцией, которая относится к этой службе или к ее среде. Как и при изменении двоичных файлов службы, т. е. при обновлении службы или ее переходе на более раннюю версию, это позволит защитить данные от повреждения (из-за ошибок) в коде приложения.

## <a name="triggering-on-demand-backup"></a>Запуск резервного копирования по запросу

Для резервного копирования по запросу требуются сведения о хранилище, чтобы отправлять файлы резервных копий. Резервное копирование по запросу будет выполняться в хранилище, указанное в политике периодического резервного копирования, или в хранилище, указанное в запросе на резервное копирование.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Резервное копирование по запросу в хранилище, указанное в политике периодического резервного копирования

Можно запросить секции Reliable Services с отслеживанием состояния или Reliable Actors для дополнительного резервного копирования по запросу в хранилище, указанное в политике периодического резервного копирования. 

Следующий пример является продолжением уже упоминавшегося в разделе о [включении периодического резервного копирования для Reliable Services с отслеживанием состояния или Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). В этом примере для секций включена политика резервного копирования и оно выполняется в службе хранилища Azure с требуемой частотой.

Резервное копирование по запросу для идентификатора секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22` может инициироваться API [BackupPartition]https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition). 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[Ход резервного копирования по запросу](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) отслеживается API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).

### <a name="on-demand-backup-to-specified-storage"></a>Резервное копирование по запросу в указанное хранилище

Резервное копирование по запросу можно запросить для секции Reliable Services с отслеживанием состояния или Reliable Actors вместе с информацией о хранилище. Информация о хранилище должна предоставляться как часть запроса на резервное копирование по запросу.

Резервное копирование по запросу для идентификатора секции `974bd92a-b395-4631-8a7f-53bd4ae9cf22` может инициировать API [BackupPartition]https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) с информацией о хранилище Azure, как показано ниже.

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

[Ход резервного копирования по запросу](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) отслеживается API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).


## <a name="tracking-on-demand-backup-progress"></a>Отслеживание хода выполнения резервного копирования по запросу

Секция Reliable Services с отслеживанием состояния или Reliable Actors может единовременно принять только один запрос на резервное копирование по запросу. Следующий запрос будет приниматься только после выполнения текущего. 

Можно выполнять несколько запросов на резервное копирование по запросу одновременно в разных секциях.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Возможные состояния для хода выполнения запроса резервного копирования по запросу:

* **Accepted** (Принято). Резервное копирование инициировано в секции и выполняется.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Success (Успешно), Failure (Сбой) или Timeout (Превышено время ожидания)**. Любое из этих состояний означает завершение запрошенного восстановления. Ниже описано значение каждого из этих состояний и подробности ответов для них.

    * **Success** (Успешно). Состояние резервного копирования _Success_ (Успешно) указывает на успешное создание резервной копии состояния секции. Ответ в этом случае возвращает параметры __BackupEpoch__ и __BackupLSN__ для секции, а также время в формате UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Failure** (Сбой). Состояние резервной копии _Failure_ (Сбой) указывает на то, что при резервном копировании состояния секции произошел сбой. Причины сбоя будут указаны в ответе на запрос.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Timeout** (Превышено время ожидания). Состояние резервной копии _Timeout_ (Превышено время ожидания) указывает на то, что не удалось создать резервную копию состояния секции в указанный промежуток времени. По умолчанию время ожидания составляет 10 минут. В этом случае рекомендуется инициировать новый запрос на резервное копирование с увеличенным значением [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout).

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>Дополнительная информация
- [Основные сведения о настройке периодического резервного копирования](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restore REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore) (Справочник по REST API службы резервного копирования и восстановления)


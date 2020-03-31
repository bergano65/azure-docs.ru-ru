---
title: Управление резервным копированием файлов Azure с помощью Rest API
description: Узнайте, как использовать REST API для управления и мониторинга файлов Azure, которые поддерживаются резервным копированием Azure.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 9d29b226aff568c91de8e1f19ddc0c64f8169e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444736"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Управление резервным копированием файлов Azure с помощью REST API

В этой статье объясняется, как выполнять задачи по управлению и мониторингу файлов Azure, которые поддерживаются [резервным копированием Azure.](https://docs.microsoft.com/azure/backup/backup-overview)

## <a name="monitor-jobs"></a>Мониторинг заданий

Служба резервного копирования Azure запускает задания, которые работают в фоновом режиме. Это включает в себя такие сценарии, как запуск резервного копирования, восстановление операций и отключение резервного копирования. Эти задания можно отслеживать с помощью идентификаторов.

### <a name="fetch-job-information-from-operations"></a>Извлечение информации о работе из операций

Операция, такая как запуск резервного копирования, всегда возвращает jobID в ответ.

Например, окончательный ответ операции [резервного копирования REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) триггера:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Задание резервного копирования файла Azure определяется полем **jobId** и может отслеживаться, как указано [здесь,](https://docs.microsoft.com/rest/api/backup/jobdetails/) с помощью запроса GET.

### <a name="tracking-the-job"></a>Отслеживание задания

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

«JobName» — это упомянутый выше «jobId». Ответ всегда "200 OK" с полем **состояния** с указанием статуса задания. После того, как он "Завершен" или "ЗавершеноС Предупреждение", **расширенныйРаздел Info** раскрывает более подробную информацию о работе.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Ответ

name  | Тип  |  Описание
--- | --- | ----
200 ОК |  JobResource  | OK

#### <a name="response-example"></a>Пример ответа

Как только *GET* URI представлен, 200 ответ возвращается.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Изменение политики

Чтобы изменить политику, с которой защищена доля файла, можно использовать тот же формат, что и включение защиты. Просто укажите новый идентификатор политики в политике запроса и отправьте запрос.

Например: Чтобы изменить политику защиты *testshare* от *schedule1* к *schedule2,* предоставьте идентификатор *schedule2* в органе запроса.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Снятие защиты с сохранением существующих данных

Вы можете удалить защиту на защищенном файле, но сохранить данные, уже резервные копии. Для этого удалите политику в органе запроса, который использовался для[включения резервного копирования,](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) и отправьте запрос. После удаления связи с политикой резервные ожидания больше не срабатывают, и новые точки восстановления не создаются.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Пример ответа

Остановка защиты для общего файла — это асинхронная операция. Операция создает еще одну операцию, которую необходимо отслеживать. Он возвращает два ответа: 202 (Принято) при создании другой операции и 200 при завершении операции.

Заголовок ответа при успешной работе принимается:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Затем отслеживайте полученную операцию с помощью заголовка местоположения или заголовка Azure-AsyncOperation с помощью команды GET:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Тело ответа

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Снятие защиты и удаление данных

Чтобы удалить защиту на защищенном файле и удалить данные резервного копирования, а также выполнить операцию удаления, как описано [здесь](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Параметры «containerName» и «protectedItemName» установлены [здесь.](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)

Следующий пример запускает операцию, чтобы остановить защиту для совместного файла *testshare,* защищенного *azurefilesvault.*

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Ответы

Защита от удаления — это асинхронная операция. Операция создает еще одну операцию, которую необходимо отслеживать отдельно.
Он возвращает два ответа: 202 (Принято) при создании другой операции и 204 (NoContent) при завершении операции.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [устранить проблемы при настройке резервного копирования для акций Azure File.](troubleshoot-azure-files.md)
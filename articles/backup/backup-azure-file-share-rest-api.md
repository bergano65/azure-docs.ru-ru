---
title: Резервное копирование акций файлов Azure с помощью REST API
description: Узнайте, как использовать REST API для резервного копирования акций файлов Azure в Хранилище служб восстановления
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248103"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Резервное копирование файла Azure совместное использование резервного копирования Azure через Rest API

В этой статье описывается, как создать резервную связь с разделом azure File с помощью резервного копирования Azure через REST API.

В этой статье предполагается, что вы уже создали хранилище служб восстановления и политику для настройки резервного копирования для вашей доли файлов. Если вы этого не сможете, обратитесь к [хранилищу и](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) [создайте](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) учебники по политике REST API для создания новых хранилищ и политик.

Для этой статьи мы будем использовать следующие ресурсы:

- **RecoveryServicesVault**: *лазуриции*

- **Политика:** *schedule1*

- **Группа ресурсов**: *лазуриты*

- **Учетная запись хранения**: *testvault2*

- **Доля файлов**: *тест-шеринг*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Настройка резервного копирования для незащищенного раздела файлов Azure с помощью REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Откройте для себя учетных записей хранения с незащищенными файлами Azure

Хранилище должно обнаружить все учетные записи хранения Azure в подписке с файлами, которые могут быть резервироваться в Хранилище служб восстановления. Это можно сделать с помощью [операции обновления](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Это асинхронная операция *POST,* которая гарантирует, что хранилище получает последний список всех незащищенных акций Azure File в текущей подписке и "кэширует" их. После "кэша" доли файла службы восстановления могут получить доступ к кадоле файла и защитить ее.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI `{subscriptionId}`имеет `{vaultName}` `{vaultresourceGroupName}`, `{fabricName}` , и параметры. В нашем примере значение для различных параметров будет следующим:

- `{fabricName}`является *Azure*

- `{vaultName}`является *лазурныйfilesvault*

- `{vaultresourceGroupName}`*лазурофайлов*

- $filter-backupManagementType eq 'AzureStorage'

Поскольку все необходимые параметры приведены в URI, нет необходимости в отдельном органе запроса.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Ответы

Операция обновления — это [асинхронная операция](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Это означает, что такая операция создает другую операцию, которая должна отслеживаться отдельно.

Он возвращает два ответа: 202 (Принято) при создании другой операции и 200 (OK) при завершении операции.

##### <a name="example-responses"></a>Примеры ответов

После отправления запроса *POST* возвращается ответ 202 (принято).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Отследите итоговую операцию, используя заголовок location с помощью простой команды *GET*.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

После обнаружения всех учетных записей хранилища Azure команда GET возвращает 200 (без содержимого). Хранилище теперь может обнаружить любую учетную запись хранения с файлами, которые могут быть подкреплены в подписке.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>Получите список учетных записей хранения, которые могут быть защищены хранилищем служб восстановления

Чтобы подтвердить, что "кэширование" сделано, перечислите все защищенные учетные записи хранения по подписке. Затем найдите желаемую учетную запись хранения в ответе. Это делается с помощью операции [GET ProtectableContainers.](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

URI *GET* имеет все необходимые параметры. Для этой операции текст запроса также не требуется.

Пример органа реагирования:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Так как мы можем найти учетную запись хранения *testvault2* в органе реагирования с дружественным названием, операция обновления, выполненная выше, была успешной. Хранилище служб восстановления теперь может успешно обнаружить учетные записи хранения с незащищенными файлами, долями в той же подписке.

### <a name="register-storage-account-with-recovery-services-vault"></a>Регистрация учетной записи хранения в хранилище услуг восстановления

Этот шаг необходим только в том случае, если вы не зарегистрировали учетную запись хранилища в хранилище ранее. Вы можете зарегистрировать хранилище через [операцию ProtectionContainers-Register.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Установите переменные для URI следующим образом:

- «ресурсGroupName» - *лазурные файлы*
- (fabricName) - *Azure*
- (vaultName) - *лазурификаций*
- «containerName» - Это атрибут имени в корпусе ответа операции GET ProtectableContainers.
   В нашем примере это *StorageContainer; Хранение; AzureFiles;testvault2*

>[!NOTE]
> Всегда возьмите атрибут имени ответа и заполните его в этом запросе. Не жестко закодивайте или создавайте формат контейнерного имени. При создании или жестком кодировании вызов API не удастся, если формат тар-имя изменится в будущем.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Тело запроса создания следующим образом:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Для получения полного перечня определений органа запроса и других деталей, обратитесь к [ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Это асинхронная операция и возвращает два ответа: "202 Принято", когда операция будет принята и "200 OK", когда операция завершена.  Чтобы отследить состояние операции, используйте заголовок местоположения, чтобы получить последний статус операции.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Пример органа реагирования при завершении операции:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Вы можете проверить, была ли регистрация успешной, из значения параметра *регистрационного статуса* в органе ответа. В нашем случае он показывает статус зарегистрированного для *testvault2,* поэтому операция регистрации прошла успешно.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Запрос всех незащищенных файлов акций под счетом хранения

Вы можете узнать о защитных товарах в учетной записи хранения с помощью операции [Protection Containers-Inquire.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) Это асинхронная операция, и результаты должны отслеживаться с помощью заголовка местоположения.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Установите переменные для вышеуказанных URI следующим образом:

- (vaultName) - *лазурификаций*
- (fabricName) - *Azure*
- «КонтейнерНоеимя» - Обратитесь к атрибуту имени в органе реагирования операции GET ProtectableContainers. В нашем примере это *StorageContainer; Хранение; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Как только запрос успешен, он возвращает код статуса "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Выберите общий файл, который необходимо создать для резервного копирования

Вы можете перечислить все защищенные элементы под подпиской и найти нужную долю файла для резервного копирования с помощью операции [GET backupprotectableItems.](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Постройте URI следующим образом:

- (vaultName) - *лазурификаций*
- ($filter- - *резервное копированиеManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Пример ответа:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Ответ содержит список всех незащищенных файлов и содержит всю информацию, требуемую службой восстановления Azure для настройки резервной системы.

### <a name="enable-backup-for-the-file-share"></a>Включить резервную капроверкасается для общего обмена файлами

После того, как соответствующая доля файлов "отождествляется" с дружественным именем, выберите политику для защиты. Чтобы узнать больше о существующих политиках в хранилище, обратитесь к [aPI-списку политики.](https://docs.microsoft.com/rest/api/backup/backuppolicies/list) Ссылаясь на имя политики, выберите [соответствующую политику](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get). См. дополнительные сведения о [создании политик](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy).

Включение защиты является асинхронной операцией *PUT,* которая создает "защищенный элемент".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Установите переменные **контейнерного имени** и **защищенного элемента,** используя атрибут Идентификатора, в корпусе отклика операции GET backupprotectable.

В нашем примере идентификатор общего файла, который мы хотим защитить, заключается в:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- «контейнерное имя» - *контейнер для хранения;хранение;лазуриты;testvault2*
- «ЗащищенныйДеталь» - *лазуридой;тестшеринг*

Или вы можете сослаться на атрибут **имени** защитного контейнера и защищенные ответы на элементы.

>[!NOTE]
>Всегда возьмите атрибут имени ответа и заполните его в этом запросе. Не жестко закодивайте или создавайте формат тар-имя или защищенный формат имени элемента. При создании или жестком кодировании вызов API не удастся, если формат названия контейнера или формат защищенного названия элемента изменится в будущем.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Создание органа запроса:

Следующий текст запроса определяет свойства, необходимые для создания защищенного элемента.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**ИсточникResourceId** является **parentcontainerFabricID** в ответ на GET резервного защитыItems.

Пример ответа

Создание защищенного элемента является асинхронной операцией, которая создает еще одну операцию, которую необходимо отслеживать. Он возвращает два ответа: 202 (Принято) при создании другой операции и 200 (OK) при завершении операции.

После отправки запроса *PUT* на создание или обновление защищенного элемента первоначальный ответ составляет 202 (принято) с заголовком местоположения.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Затем отслеживайте полученную операцию с помощью заголовка местоположения или заголовка Azure-AsyncOperation с помощью команды *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Завершенная операция возвращает код 200 (ОК) с содержимым защищенного элемента в тексте ответа.

Пример органа реагирования:

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

Это подтверждает, что защита включена для общего файла и первая резервная часть будет запущена в соответствии с графиком политики.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Триггер резервного копирования по требованию для общего элемента файла

После настройки элемента файла Azure для резервного копирования резервные ожидания запускаются в соответствии с графиком политики. Вы можете дождаться первого запланированного резервного копирования или активировать эту операцию по требованию в любое время.

Активация резервного копирования по запросу — это операция POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

«containerName» и «protectedItemName» построены выше при включении резервного копирования. В нашем примере мы получим следующее:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Создание текста запроса

Чтобы активировать резервное копирование по запросу, используйте компоненты текста запроса.

| name       | Тип                       | Описание                       |
| ---------- | -------------------------- | --------------------------------- |
| Свойства | AzurefilesharebackupReques | Свойства резервного копированияRequestResource |

Полный список определений в тексте запроса и другие сведения см. в [документации по активации резервного копирования для защищенных элементов для REST API](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

Пример органа запроса

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Ответы

Активация резервного копирования по запросу — это [асинхронная операция](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Это означает, что такая операция создает другую операцию, которая должна отслеживаться отдельно.

Он возвращает два ответа: 202 (Принято) при создании другой операции и 200 (OK) при завершении операции.

### <a name="example-responses"></a>Примеры ответов

После отправки запроса *POST* для выполнения резервного копирования по запросу будет получен первоначальный ответ 202 (принято) с заголовками location или Azure-AsyncOperation.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Затем отслеживайте полученную операцию с помощью заголовка местоположения или заголовка Azure-AsyncOperation с помощью команды *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Завершенная операция возвращает код 200 (ОК) с идентификатором результата задания резервного копирования в тексте ответа.

#### <a name="sample-response-body"></a>Образец текста ответа

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Так как задание резервного копирования — это длительная операция, оно должно отслеживаться, как описано в [документации по мониторингу заданий для REST API](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [восстановить файлы Azure с помощью Rest API.](restore-azure-file-share-rest-api.md)

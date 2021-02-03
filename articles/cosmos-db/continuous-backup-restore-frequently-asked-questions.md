---
title: Часто задаваемые вопросы о функции восстановления на момент времени Azure Cosmos DB.
description: В этой статье перечислены часто задаваемые вопросы о функции восстановления на момент времени Azure Cosmos DB, которая достигается с помощью режима непрерывной архивации.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 7a41eb8bdd573ac08b0c76eb9a6c2b0724637c39
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527962"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature"></a>Часто задаваемые вопросы о функции восстановления Azure Cosmos DB на момент времени
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

В этой статье перечислены часто задаваемые вопросы о функции восстановления на момент времени Azure Cosmos DB, которая достигается с помощью режима непрерывной архивации.

## <a name="how-much-time-does-it-takes-to-restore"></a>Сколько времени требуется для восстановления?
Длительность восстановления зависит от размера данных.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Можно ли отправить время восстановления по местному времени?
Восстановление может не происходить в зависимости от того, существовали ли в это время ключевые ресурсы, такие как базы данных или контейнеры. Проверить можно, введя время и просмотрев выбранную базу данных или контейнер на определенный момент времени. Если не существует ресурсов для восстановления, процесс восстановления не работает.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Как можно отвести себя о восстановлении учетной записи?
После отправки команды RESTORE и перехода на ту же страницу после завершения операции в строке состояния отобразится сообщение об успешном восстановлении учетной записи. Можно также выполнить поиск восстановленной учетной записи и [отвести состояние восстанавливаемой учетной записи](continuous-backup-restore-portal.md#track-restore-status). Пока выполняется восстановление, состояние учетной записи будет "создание". После завершения операции восстановления состояние учетной записи изменится на "в сети".

Аналогично PowerShell и CLI можно отслеживать ход выполнения операции восстановления, выполняя `az cosmosdb show` команду следующим образом:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

ProvisioningState показывает "успех", если учетная запись находится в режиме "в сети".

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Как узнать, восстановлена ли учетная запись из другой учетной записи?
Выполните `az cosmosdb show` команду в выходных данных, чтобы увидеть, что свойство имеет значение `createMode` . Значение, если задано **Восстановление**. Это означает, что учетная запись была восстановлена из другой учетной записи. `restoreParameters`Свойство имеет дополнительные сведения `restoreSource` , например, с идентификатором исходной учетной записи. Последним идентификатором GUID в `restoreSource` параметре является InstanceId исходной учетной записи.

Например, в следующих выходных данных идентификатор экземпляра исходной учетной записи — "7b4bb-f6a0-430E-ade1-638d781830cc".

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Что происходит при восстановлении общей пропускной способности базы данных или контейнера в общей пропускной способности?
Восстанавливается вся база данных с общей пропускной способностью. Нельзя выбрать подмножество контейнеров в базе данных с общей пропускной способностью для восстановления.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Каково использование InstanceID в определении учетной записи?
В любой момент времени свойство "accountName" учетной записи Azure Cosmos DB является глобально уникальным, пока оно активно. Однако после удаления учетной записи можно создать другую учетную запись с тем же именем, поэтому "accountName" больше не сможет опознать экземпляр учетной записи. 

ID или "instanceId" — это свойство экземпляра учетной записи, которое используется для устранения неоднозначности в нескольких учетных записях (активных и удаленных), если они имеют одно и то же имя для восстановления. Идентификатор экземпляра можно получить, выполнив `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` команды или. Значение атрибута Name обозначает "InstanceID".

## <a name="next-steps"></a>Следующие шаги

* Что такое режим [непрерывного резервного копирования](continuous-backup-restore-introduction.md) ?
* Настройка непрерывного резервного копирования и управление им с помощью [портал Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [интерфейса командной строки](continuous-backup-restore-command-line.md)или [Azure Resource Manager](continuous-backup-restore-template.md).
* [Управление разрешениями](continuous-backup-restore-permissions.md) , необходимыми для восстановления данных в режиме непрерывной архивации.
* [Модель ресурсов режима непрерывного резервного копирования](continuous-backup-restore-resource-model.md)
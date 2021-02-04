---
title: Используйте шаблон ARM для настройки непрерывного резервного копирования и восстановления на момент времени в Azure Cosmos DB.
description: Узнайте, как подготавливать учетную запись с непрерывным резервным копированием и восстановлением данных с помощью шаблонов Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 642c61414d882b9cfe83f585fda8ff5404e8834a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538482"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Настройка непрерывного резервного копирования и восстановление на момент времени (Предварительная версия) и управление ими с помощью шаблонов Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Функция восстановления на момент времени (режим непрерывного резервного копирования) для Azure Cosmos DB в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Функция восстановления на момент времени (Предварительная версия) Azure Cosmos DB позволяет восстановиться после случайного изменения в контейнере, восстановления удаленной учетной записи, базы данных или контейнера или восстановления в любом регионе (где существовали резервные копии). Режим непрерывного резервного копирования позволяет выполнять восстановление на любой момент времени в течение последних 30 дней.

В этой статье описывается, как подготавливать учетную запись с непрерывным резервным копированием и восстановлением данных с помощью шаблонов Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Предоставление учетной записи с непрерывным резервным копированием

Azure Resource Manager шаблоны можно использовать для развертывания Azure Cosmos DB учетной записи с непрерывным режимом. При определении шаблона для предоставления учетной записи включите параметр "Баккупполици", как показано в следующем примере:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Затем разверните шаблон с помощью Azure PowerShell или CLI. В следующем примере показано, как развернуть шаблон с помощью команды CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Восстановление с помощью шаблона диспетчер ресурсов

Вы также можете восстановить учетную запись с помощью шаблона диспетчер ресурсов. При определении шаблона включите следующие параметры:

* Присвойте параметру "createMode" значение "Restore"
* Определите "Ресторепараметерс", обратите внимание, что значение "Ресторесаурце" извлекается из выходных данных `az cosmosdb restorable-database-account list` команды для исходной учетной записи. Атрибут идентификатора экземпляра для имени учетной записи используется для восстановления.
* Задайте для параметра "Режимвосстановления" значение "PointInTime" и настройте параметр "Ресторетиместампинутк".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Затем разверните шаблон с помощью Azure PowerShell или CLI. В следующем примере показано, как развернуть шаблон с помощью команды CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Следующие шаги

* Настройка непрерывного резервного копирования и управление им с помощью [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)или [портал Azure](continuous-backup-restore-portal.md).
* [Модель ресурсов режима непрерывного резервного копирования](continuous-backup-restore-resource-model.md)
* [Управление разрешениями](continuous-backup-restore-permissions.md) , необходимыми для восстановления данных в режиме непрерывной архивации.
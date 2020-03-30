---
title: Аудит учетной записи хранилища за VNet и брандмауэром
description: Настройка аудита для записи событий базы данных на учетной записи хранилища за виртуальной сетью и брандмауэром
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387637"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Напишите аудит на учетную запись хранилища за VNet и брандмауэром

Аудит [для базы данных Azure S'L](sql-database-technical-overview.md) и [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) поддерживает написание событий базы данных в [учетную запись хранилища Azure](../storage/common/storage-account-overview.md) за виртуальной сетью и брандмауэром. 

В этой статье объясняется два способа настройки учетной записи хранилища Azure S'L Server и Azure для этой опции. Первый использует портал Azure, второй — REST.

### <a name="background"></a>Историческая справка

[Виртуальная сеть Azure (VNet)](../virtual-network/virtual-networks-overview.md) является основным строительным блоком для вашей частной сети в Azure. Виртуальная сеть позволяет ресурсам Azure различных типов (например, виртуальным машинам Azure) обмениваться данными друг с другом через локальные сети и через Интернет. VNet похож на традиционную сеть в собственном центре обработки данных, но приносит с собой дополнительные преимущества инфраструктуры Azure, такие как масштаб, доступность и изоляция.

Чтобы узнать больше о концепциях VNet, рекомендациях практики и многом других, [см.](../virtual-network/virtual-networks-overview.md)

Подробнее о том, как создать виртуальную сеть, можно узнать: [«Быстрый старт: Создайте виртуальную сеть с помощью портала Azure».](../virtual-network/quick-create-portal.md)

## <a name="prerequisites"></a>Предварительные требования

Для записи аудита на учетную запись хранилища за VNet или брандмауэром требуются следующие предпосылки:

> [!div class="checklist"]
> * Учетная запись v2 общего назначения. Если у вас есть учетная запись хранения v1 общего назначения или blob, [переймите до учетной записи хранения v2 общего назначения.](../storage/common/storage-account-upgrade.md) Для получения дополнительной [информации см.](../storage/common/storage-account-overview.md#types-of-storage-accounts)
> * Учетная запись хранилища должна находиться в той же подписке и в том же месте, что и сервер базы данных Azure S'L. 
> * Учетная запись хранения `Allow trusted Microsoft services to access this storage account`Azure требует . Установите это на складе **брандмауэров и виртуальных сетей.**
> * У вас `Microsoft.Authorization/roleAssignments/write` должно быть разрешение на выбранный счет хранения. Для получения дополнительной информации смотрите [встроенные роли Azure.](../role-based-access-control/built-in-roles.md)

## <a name="configure-in-azure-portal"></a>Настройка на портале Azure

Подключитесь к [порталу Azure](https://portal.azure.com) с подпиской. Перейдите к группе ресурсов и серверу баз данных Azure S'L.

1. Нажмите на **аудит** под заголовком Безопасности. Выберите **на**.

2. Выберите пункт **Служба хранилища**. Выберите учетную запись хранения, в которой будут сохранены журналы. Учетная запись хранилища должна соответствовать требованиям, перечисленным в [Prerequisites.](#prerequisites)

3. Сведения **об открытых хранилищах** 

  > [!NOTE]
  > Если выбранная учетная запись хранилища находится за VNet, вы увидите следующее сообщение:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Если вы не видите это сообщение, то учетная запись хранилища не стоит за VNet.

4. Выберите количество дней для периода удержания. После этого щелкните **OK**. Логи старше периода хранения удаляются.

5. Выберите **параметры аудита.**

Вы успешно настроили аудит для записи на учетную запись хранилища за VNet или брандмауэром. 

## <a name="configure-with-rest-commands"></a>Настройка с помощью команд REST

В качестве альтернативы использованию портала Azure можно использовать команды REST для настройки аудита для записи событий базы данных на учетной записи хранилища за VNet и Firewall. 

Пример скриптов в этом разделе требует обновления скрипта перед запуском. Замените в сценарии следующие значения.

|Образец значения|Описание образца|
|:-----|:-----|
|`<subscriptionId>`| Идентификатор подписки Azure.|
|`<resource group>`| Группа ресурсов|
|`<sql database server>`| Имя сервера Базы данных SQL Azure|
|`<administrator login>`| Учетная запись администратора базы данных S'L |
|`<complex password>`| Сложный пароль для учетной записи администратора|

Для настройки аудита S'L для записи событий в учетную запись хранилища за VNet или Firewall:

1. Зарегистрируйте сервер базы данных Azure S'L с помощью Active Directory Azure (Azure AD). Используйте aPI PowerShell или REST.

   **PowerShell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Пример запроса

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Тело запроса

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Откройте [портал Azure](https://portal.azure.com). Войдите в свою учетную запись хранения. Найдите **элемент управления доступом (IAM)** и нажмите **«Добавить» назначение ролей.** Назначайте роль **вкладчика в хранилище данных Blob** Data RBAC вашему серверу Azure S'L, в которой размещена база данных Azure S'L, зарегистрированная в Azure Active Directory (Azure AD), как и на предыдущем этапе.

   > [!NOTE]
   > Этот шаг могут выполнять только участники с правами владельца. Для различных встроенных ролей для ресурсов Azure отсылай к [встроенным ролям Azure.](../role-based-access-control/built-in-roles.md)

3. Навлаживайте [политику аудита кабо-аудита сервера Azure S'L,](/rest/api/sql/server%20auditing%20settings/createorupdate)не указывая *на хранилищеAccountAccessKey:*

   Пример запроса

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Тело запроса

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Дальнейшие действия

- [Используйте PowerShell для создания виртуальной точки управления сетевой службой, а затем виртуального сетевого правила для базы данных Azure S'L.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Правила виртуальной сети: операции с REST AIS](/rest/api/sql/virtualnetworkrules)
- [Использование конечных точек служб и правил виртуальной сети для серверов базы данных](sql-database-vnet-service-endpoint-rule-overview.md)

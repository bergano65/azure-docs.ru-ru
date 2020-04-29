---
title: Аудит учетной записи хранения за виртуальной сетью и брандмауэром
description: Настройка аудита для записи событий базы данных в учетной записи хранения за виртуальной сетью и брандмауэром
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387637"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Запись аудита в учетную запись хранения за пределами виртуальной сети и брандмауэра

Аудит для [базы данных SQL Azure](sql-database-technical-overview.md) и [Azure синапсе Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) поддерживает запись событий базы данных в [учетную запись хранения Azure](../storage/common/storage-account-overview.md) за пределами виртуальной сети и брандмауэра. 

В этой статье описаны два способа настройки SQL Server Azure и учетной записи хранения Azure для этого параметра. Первый использует портал Azure, второй использует ОСТАВШУЮся.

### <a name="background"></a>История

[Виртуальная сеть Azure (VNet)](../virtual-network/virtual-networks-overview.md) — это фундаментальный Стандартный блок для частной сети в Azure. Виртуальная сеть позволяет ресурсам Azure различных типов (например, виртуальным машинам Azure) обмениваться данными друг с другом через локальные сети и через Интернет. Виртуальная сеть аналогична традиционной сети в собственном центре обработки данных, но предоставляет дополнительные преимущества инфраструктуры Azure, такие как масштабирование, доступность и изоляция.

Дополнительные сведения о концепциях виртуальной сети, рекомендации и многое другое см. в статье [что такое виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md).

Дополнительные сведения о создании виртуальной сети см. в разделе Краткое руководство. [Создание виртуальной сети с помощью портал Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Предварительные условия

Для записи аудита в учетную запись хранения за виртуальной сетью или брандмауэром необходимо выполнить следующие предварительные требования.

> [!div class="checklist"]
> * Учетная запись хранения общего назначения версии 2. При наличии учетной записи общего назначения версии v1 или хранилища BLOB-объектов [Обновите учетную запись хранения общего назначения v2](../storage/common/storage-account-upgrade.md). Дополнительные сведения см. в разделе [типы учетных записей хранения](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Учетная запись хранения должна находиться в той же подписке и в том же расположении, что и сервер базы данных SQL Azure. 
> * Требуется `Allow trusted Microsoft services to access this storage account`учетная запись хранения Azure. Задайте это значение для брандмауэров учетной записи хранения **и виртуальных сетей**.
> * Необходимо иметь `Microsoft.Authorization/roleAssignments/write` разрешение на выбранную учетную запись хранения. Дополнительные сведения см. [в статье встроенные роли Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Настройка на портале Azure

Подключитесь к [портал Azure](https://portal.azure.com) с помощью подписки. Перейдите к группе ресурсов и серверу базы данных SQL Azure.

1. Щелкните **Аудит** под заголовком безопасность. Выберите **вкл**.

2. Выберите пункт **Служба хранилища**. Выберите учетную запись хранения, в которой будут сохраняться журналы. Учетная запись хранения должна соответствовать требованиям, перечисленным в [предварительных](#prerequisites)требованиях.

3. Открыть **сведения о хранилище** 

  > [!NOTE]
  > Если выбранная учетная запись хранения находится за виртуальной сетью, вы увидите следующее сообщение:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Если это сообщение не отображается, то учетная запись хранения не находится за виртуальной сетью.

4. Выберите количество дней для срока хранения. После этого щелкните **OK**. Журналы старше, чем срок хранения, удаляются.

5. Выберите **сохранить** в параметрах аудита.

Вы успешно настроили аудит для записи в учетную запись хранения за виртуальной сетью или брандмауэром. 

## <a name="configure-with-rest-commands"></a>Настройка с помощью команд RESTFUL

В качестве альтернативы использованию портал Azure можно использовать команды RESTFUL, чтобы настроить аудит для записи событий базы данных в учетную запись хранения за виртуальной сетью и брандмауэром. 

В примерах сценариев, приведенных в этом разделе, требуется обновить скрипт перед выполнением. Замените в сценарии следующие значения.

|Образец значения|Пример описания|
|:-----|:-----|
|`<subscriptionId>`| Идентификатор подписки Azure.|
|`<resource group>`| Группа ресурсов|
|`<sql database server>`| Имя сервера Базы данных SQL Azure|
|`<administrator login>`| Учетная запись администратора базы данных SQL |
|`<complex password>`| Сложный пароль для учетной записи администратора|

Чтобы настроить аудит SQL для записи событий в учетную запись хранения за виртуальной сетью или брандмауэром, выполните следующие действия.

1. Зарегистрируйте сервер базы данных SQL Azure с помощью Azure Active Directory (Azure AD). Используйте либо PowerShell, либо REST API.

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

2. Откройте [портал Azure](https://portal.azure.com). Войдите в свою учетную запись хранения. Выберите **Управление доступом (IAM)** и щелкните **добавить назначение ролей**. Назначьте роль RBAC **участника данных BLOB-объекта хранилища** в Azure SQL Server, где размещена база данных SQL Azure, зарегистрированная в Azure Active Directory (Azure AD), как на предыдущем шаге.

   > [!NOTE]
   > Этот шаг могут выполнять только участники с правами владельца. Сведения о различных встроенных ролях для ресурсов Azure см. в статье [встроенные роли Azure](../role-based-access-control/built-in-roles.md).

3. Настройка [политики аудита больших двоичных объектов Azure SQL Server](/rest/api/sql/server%20auditing%20settings/createorupdate)без указания *сторажеаккаунтакцесскэй*:

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

## <a name="next-steps"></a>Дальнейшие шаги

- [С помощью PowerShell создайте конечную точку службы виртуальной сети, а затем правило виртуальной сети для базы данных SQL Azure.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Правила виртуальной сети. операции с помощью API-интерфейсов RESTFUL](/rest/api/sql/virtualnetworkrules)
- [Использование конечных точек служб и правил виртуальной сети для серверов базы данных](sql-database-vnet-service-endpoint-rule-overview.md)

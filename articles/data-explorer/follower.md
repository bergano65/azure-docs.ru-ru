---
title: Использование функции базы данных следующих компонентов для присоединения баз данных в Azure обозреватель данных
description: Узнайте, как присоединить базы данных в Azure обозреватель данных с помощью функции базы данных ниже.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: a46cf78d902ec8391d7dc3667a6d66daa78927ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828569"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Использование базы данных следующих служб для присоединения баз данных в Azure обозреватель данных

Функция **базы данных-след** позволяет присоединить базу данных, расположенную в другом кластере, к кластеру Azure обозреватель данных. **База данных-след** присоединяется в режиме *только для чтения* , что позволяет просматривать данные и выполнять запросы к данным, которые были переданы в **базу данных лидера**. База данных следов синхронизирует изменения в базах данных лидера. В связи с синхронизацией данные в доступности данных забывают в течение нескольких секунд. Продолжительность временной задержки зависит от общего размера метаданных базы данных лидера. Ведущие и последующие базы данных используют одну и ту же учетную запись хранения для выборки данных. Владельцем хранилища является лидер базы данных. База данных для получения результатов просматривает данные без необходимости их приема. Поскольку присоединенная база данных является базой данных только для чтения, данные, таблицы и политики в базе данных нельзя изменить, за исключением [политики кэширования](#configure-caching-policy), [участников](#manage-principals)и [разрешений](#manage-permissions). Не удается удалить подключенные базы данных. Они должны быть отсоединены от лидера или следующего, и только после этого их можно удалить. 

Присоединение базы данных к другому кластеру с помощью возможности последующей функции используется в качестве инфраструктуры для обмена данными между организациями и группами. Эта функция полезна для разделения ресурсов вычислений для защиты рабочей среды от нерабочих случаев использования. Кроме того, можно использовать для связи стоимости кластера Azure обозреватель данных с стороной, которая выполняет запросы к данным.

## <a name="which-databases-are-followed"></a>Какие базы данных будут выполнены?

* Кластер может соответствовать одной базе данных, нескольким базам данных или всем базам данных в кластере лидера. 
* Один кластер может следовать за базами данных из нескольких серверов-заполнителей. 
* Кластер может содержать как базы данных, так и лидерическую базу данных

## <a name="prerequisites"></a>Предварительные требования

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
1. [Создайте кластер и базу данных](/azure/data-explorer/create-cluster-database-portal) для лидера и последующих результатов.
1. Прием [данных](/azure/data-explorer/ingest-sample-data) в лидер базы данных с помощью одного из различных методов, обсуждаемых в [обзоре приема](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Присоединение базы данных

Существует несколько методов, которые можно использовать для присоединения базы данных. В этой статье обсуждается присоединение базы данных с C# помощью или шаблона Azure Resource Manager. Чтобы присоединить базу данных, необходимо иметь разрешения на доступ к кластеру лидера и к кластеру последующих узлов. Дополнительные сведения о разрешениях см. в разделе [Управление разрешениями](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Присоединение базы данных с помощьюC#

**Необходимые NuGet**

* Установите [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft. RESTful. ClientRuntime. Azure. Authentication для проверки подлинности](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Присоединение базы данных с помощью шаблона Azure Resource Manager

В этом разделе вы узнаете, как присоединить базу данных с помощью [шаблона Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Развертывание шаблона 

Шаблон Azure Resource Manager можно развернуть с [помощью портал Azure](https://portal.azure.com) или с помощью PowerShell.

   ![Шаблоны развертывания](media/follower/template-deployment.png)


|**Параметр**  |**Описание**  |
|---------|---------|
|Имя кластера следов     |  Имя кластера последов       |
|Имя конфигурации присоединенной базы данных    |    Имя объекта конфигурации присоединенной базы данных. Имя должно быть уникальным на уровне кластера.     |
|Имя базы данных     |      Имя базы данных, которая будет следовать. Если вы хотите следовать всем базам данных лидера, используйте "*".   |
|Идентификатор ресурса кластера лидера    |   Идентификатор ресурса для кластера лидера.      |
|Тип изменения участников по умолчанию    |   Тип изменения субъекта по умолчанию. Может быть `Union`, `Replace` или `None`. Дополнительные сведения об изменении типа субъекта по умолчанию см. в разделе [команда управления типа изменения основного сервера](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Расположение   |   Расположение всех ресурсов. Лидер и след должны находиться в одном расположении.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Проверка успешного присоединения базы данных

Чтобы убедиться, что база данных была успешно присоединена, найдите подключенные базы данных в [портал Azure](https://portal.azure.com). 

1. Перейдите к кластеру последующих узлов и выберите **базы данных** .
1. Поиск новых баз данных, которые доступны только для чтения в списке

    ![База данных, доступная только для чтения](media/follower/read-only-follower-database.png)

Еще один вариант:

1. Перейдите к кластеру лидерства и выберите **базы данных** .
2. Убедитесь, что соответствующие базы данных помечены как **Общие для других** > **Да**

    ![Чтение и запись подключенных баз данных](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Отсоедините базу данных следующих результатов с помощьюC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Отсоединение присоединенной базы данных последующих действий от кластера

Кластер следов может отключить любую присоединенную базу данных следующим образом:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Отсоединение присоединенной базы данных следующих серверов от ведущего кластера

Кластер лидера может отключить любую присоединенную базу данных следующим образом:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Управление участниками, разрешениями и политикой кэширования

### <a name="manage-principals"></a>Управление участниками

При присоединении базы данных укажите **тип изменения участников по умолчанию "** . По умолчанию сбор данных о лидере [полномочных участников](/azure/kusto/management/access-control/index.md#authorization) сохраняется.

|**Вид** |**Описание**  |
|---------|---------|
|**Наборов**     |   Присоединенные участники базы данных всегда включают в себя исходные субъекты базы данных, а также дополнительные новые участники, добавленные в базу данных следующих.      |
|**Замените**   |    Нет наследования субъектов от исходной базы данных. Для присоединенной базы данных необходимо создать новые субъекты. По крайней мере один участник должен быть добавлен к наследованию субъекта-блока.     |
|**None**   |   Присоединенные участники базы данных включают только субъекты исходной базы данных без дополнительных субъектов.      |

Дополнительные сведения об использовании команд управления для настройки полномочных участников см. в разделе [Управление командами для управления кластером последующих действий](/azure/kusto/management/cluster-follower.md).

### <a name="manage-permissions"></a>Управление разрешениями

Управление разрешением на доступ к базе данных только для чтения осуществляется так же, как и для всех типов баз данных. См. раздел [Управление разрешениями в портал Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Настройка политики кэширования

Администратор базы данных может изменить [политику кэширования](/azure/kusto/management/cache-policy) присоединенной базы данных или любой из ее таблиц в кластере размещения. По умолчанию заполнение коллекции баз данных и политик кэширования на уровне таблицы. Например, можно использовать политику кэширования в 30 дней в базе данных лидера для выполнения ежемесячного создания отчетов и политику кэширования в течение трех дней в базе данных последующих версий, чтобы запросить только последние данные для устранения неполадок. Дополнительные сведения об использовании команд управления для настройки политики кэширования для базы данных или следующей таблицы см. в разделе [Управление командами для управления кластером последующих действий](/azure/kusto/management/cluster-follower.md).

## <a name="limitations"></a>Ограничения

* Следующие и ведущие кластеры должны находиться в одном регионе.
* Прием [потоковой передачи](/azure/data-explorer/ingest-data-streaming) нельзя использовать для базы данных, которая находится в процессе.
* Невозможно удалить базу данных, присоединенную к другому кластеру, прежде чем отсоединить ее.
* Невозможно удалить кластер с базой данных, присоединенной к другому кластеру, прежде чем отсоединить его.
* Невозможно прикрепить к кластеру, присоединенному к исполнению или выполнительу базы данных. 

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о конфигурации кластера ниже см. в разделе [управляющие команды для управления кластером последующих действий](/azure/kusto/management/cluster-follower.md).
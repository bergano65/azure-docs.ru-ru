---
title: Журнал ведения диагностики в Azure Cosmos DB
description: Сведения о различных способах отслеживания и записи в журнал данных, хранящихся в Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a2702abd39ecdf506d58b6bd8884f12607e29e8
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615302"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Журнал ведения диагностики в Azure Cosmos DB 

После того как вы начнете использовать одну или несколько баз данных Azure Cosmos, вам может потребоваться отслеживать, как и когда осуществляется доступ к базам данных. В этой статье представлен обзор всех журналов, доступных на платформе Azure, Вы узнаете, как включить ведение журналов диагностики для мониторинга, чтобы отправить журналы в службу [хранилища Azure](https://azure.microsoft.com/services/storage/), как выполнять потоковую передачу журналов в [концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/)и экспортировать журналы в [журналы Azure Monitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Журналы, доступные в Azure

Прежде чем говорить о том, как отслеживать учетную запись Azure Cosmos DB, давайте ознакомимся с некоторыми сведениями о ведении журналов и мониторинге. На платформе Azure существуют различные типы журналов. Например, [журналы действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [журналы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [метрики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), события, мониторинг пульса, журналы операций и т. д. Существует множество журналов. Полный список журналов можно просмотреть в [Azure Monitor журналов](https://azure.microsoft.com/services/log-analytics/) в портал Azure. 

На следующем рисунке показаны различные доступные журналы Azure:

![Различные виды журналов Azure](./media/logging/azurelogging.png)

На приведенном выше рисунке **вычислительные ресурсы** представляют ресурсы Azure, для которых можно получить доступ к гостевой ОС Майкрософт. Например, к вычислительным ресурсам относятся виртуальные машины Azure, масштабируемые наборы виртуальных машин, Служба контейнеров Azure и т. д. Для вычислительных ресурсов создаются журналы действий, журналы диагностики и журналы приложений. Дополнительные сведения см. в статье [Источники данных мониторинга в Azure](../azure-monitor/platform/data-sources.md).

**Невычислительными** считаются ресурсы, в которых не позволяют напрямую обратиться к базовой ОС, и работа проводится непосредственно с ресурсом. Например, группы безопасности сети, Logic Apps и т. д. Azure Cosmos DB — это невычислительный ресурс. Журналы для невычислительных ресурсов можно просмотреть в журнале действий. Можно также включить параметр "Журналы диагностики" на портале. Дополнительные сведения см. в статье [Источники данных для мониторинга Azure](../azure-monitor/platform/data-sources.md).

В журнале действий регистрируются операции на уровне подписки для Azure Cosmos DB. В него записываются такие операции, как ListKeys, Write DatabaseAccounts и т. д. Журналы диагностики содержат более подробные сведения. Они позволяют записывать операции DataPlaneRequests (Create, Read, Query и т. д.) и MongoRequests.


В этой статье мы рассмотрим журнал действий Azure, журналы диагностики Azure и метрики Azure. Ниже приведены различия между этими тремя журналами. 

### <a name="azure-activity-log"></a>Журнал действий Azure

Журнал действий Azure — это журнал подписки с подробными сведениями о событиях на уровне подписки, которые произошли в Azure. Журнал действий сообщает о событиях уровня управления в административной категории. С помощью журнала изменений можно ответить на вопросы "что? кто? когда?" о любой операции записи (PUT, POST, DELETE) с ресурсами в вашей подписке. Вы также можете отслеживать состояние операции и другие ее свойства. 

Журналы действий отличаются от журналов диагностики. Журнал действий предоставляет внешние данные об операциях с ресурсами (_уровня управления_). В рамках Azure Cosmos DB операции уровня управления включают в себя создание контейнеров, отображение списка ключей, удаление ключей, отображение списка баз данных и т. д. Журналы диагностики выдаются ресурсом и содержат данные о его работе (_плоскость данных_). Примерами некоторых операций уровня данных в журнале диагностики являются Delete, Insert и ReadFeed.

Журналы действий (операции уровня управления) могут быть более насыщенными и включать в себя полный адрес электронной почты вызывающего объекта, IP-адрес вызывающего объекта, имя ресурса, имя операции, идентификатор клиента и многое другое. Журнал действий содержит несколько [категорий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) данных. Подробные сведения см. в статье [Схема событий журнала действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Тем не менее из журналов диагностики могут часто удаляться пользовательские сведения. У вас может быть IP-адрес вызывающего объекта, но в нем будет отсутствовать последний октет.

### <a name="azure-metrics"></a>Метрики Azure

Наиболее важным типом данных [метрик Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) являются данные телеметрии Azure (также называемые _счетчиками производительности_), которые выдаются большинством ресурсов Azure. Метрики позволяют просматривать сведения о пропускной способности, хранилище, согласованности, доступности и задержке ресурсов Azure Cosmos DB. Дополнительные сведения см. в статье [Мониторинг и отладка с помощью метрик в Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Журналы диагностики Azure

Журналы диагностики Azure выдаются ресурсом. Они содержат подробные и своевременные данные о работе этого ресурса. Эти журналы фиксируются для каждого запроса. Содержимое этих журналов зависит от типа ресурса. Журналы диагностики уровня ресурса также отличаются от журналов диагностики уровня гостевой ОС. Журналы диагностики гостевой ОС собираются агентом, который работает внутри виртуальной машины или поддерживаемого ресурса другого типа. Для сбора журналов диагностики уровня ресурса не требуется агент. В них записываются данные ресурсов с платформы Azure. В журналы диагностики уровня гостевой ОС записываются данные из операционной системы и приложений, выполняющихся на виртуальной машине.

![Процесс ведения журнала диагностики в хранилище, Центрах событий и журналах Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Для каких данных применяется ведение журналов в журналах диагностики Azure?

* Все прошедшие проверку подлинности внутренние запросы (TCP/REST) во всех API, включая запросы, завершившиеся сбоем из-за недостаточных разрешений на доступ, системных ошибок или неправильных запросов. Запросы Graph, Cassandra и API таблиц, инициируемые пользователем, в настоящее время не поддерживаются.
* Операции в самой базе данных, включая операции CRUD во всех документах, контейнерах и базах данных.
* Операции с ключами учетной записи, включая создание, изменение или удаление ключей.
* непроверенные запросы, которые приводят к появлению ответа 401 (например, запросы без токена носителя, с недействительным токеном, а также запросы неправильного формата или просроченные запросы).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Включение ведения журнала на портале Azure

Чтобы включить ведение журнала диагностики в портал Azure, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). 

1. Перейдите к своей учетной записи Azure Cosmos. Откройте панель **параметры диагностики** и выберите пункт Добавить параметр **диагностики** .

    ![Включение журнала ведения диагностики для Azure Cosmos DB на портале Azure](./media/logging/turn-on-portal-logging.png)

1. На странице **параметры диагностики** заполните форму следующими сведениями: 

    * **Имя.** Введите имя для создаваемых журналов.

    * Журналы можно хранить в следующих службах:

      * **Архивировать в учетной записи хранения**. Чтобы использовать этот параметр, необходима учетная запись хранения для подключения. Чтобы создать новую учетную запись хранения на портале, см. статью [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md) . Затем вернитесь в область параметры диагностики Azure Cosmos DB на портале, чтобы выбрать учетную запись хранения. Возможно, потребуется подождать несколько минут, пока созданная учетная запись хранения отобразится в раскрывающемся меню.

      * **Передать в концентратор событий.** Чтобы использовать этот параметр, вам понадобится пространство имен Центров событий и концентратор событий для подключения. Чтобы создать пространство имен Центров событий, ознакомьтесь со статьей [Создание пространства имен Центров событий и концентратора событий с помощью портала Azure](../event-hubs/event-hubs-create.md). Затем вернитесь на эту страницу на портале, чтобы выбрать пространство имен концентратора событий и имя политики.

      * **Отправить в Log Analytics**. Чтобы использовать этот параметр, воспользуйтесь одной из имеющихся рабочих областей или создайте рабочую область Log Analytics, следуя инструкциям в разделе [Создание рабочей области](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) на портале. 

   * Можно вести журнал следующих данных:

      * **DataPlaneRequests**: Выберите этот параметр, чтобы регистрировать запросы серверной части для всех API-интерфейсов, в том числе учетные записи SQL, Graph, MongoDB, Cassandra и API таблиц в Azure Cosmos DB. Если выполняется архивация в учетную запись хранения, можно выбрать период хранения журналов диагностики. По окончании периода хранения журналы удаляются автоматически. Следующие данные JSON представляют собой пример выходных данных, регистрируемых с помощью DataPlaneRequests. Ключевые свойства для заметок: Requestcharge, statusCode, clientIPaddress и partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Выберите этот параметр, чтобы регистрировать инициированные пользователем запросы от внешнего интерфейса для обслуживания запросов к API Azure Cosmos DB для MongoDB. Запросы MongoDB будут отображаться в MongoRequests, а также в DataPlaneRequests. Если выполняется архивация в учетную запись хранения, можно выбрать период хранения журналов диагностики. По окончании периода хранения журналы удаляются автоматически. Следующие данные JSON представляют собой пример выходных данных, регистрируемых с помощью MongoRequests. Ключевые свойства для заметок: Requestcharge, код операции:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **Куерирунтиместатистикс**: Выберите этот параметр, чтобы регистрировать выполненный текст запроса.  Следующие данные JSON представляют собой пример выходных данных, регистрируемых с помощью Куерирунтиместатистикс:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **Metric Requests** (Запросы метрик). Выберите этот параметр, чтобы хранить подробные данные в [метриках Azure](../azure-monitor/platform/metrics-supported.md). Если выполняется архивация в учетную запись хранения, можно выбрать период хранения журналов диагностики. По окончании периода хранения журналы удаляются автоматически.

3. Щелкните **Сохранить**.

    Если отобразится сообщение об ошибке Failed to update diagnostics for \<workspace name>. The subscription \<subscription id&gt; is not registered to use microsoft.insights (Не удалось обновить данные диагностики для &lt;имя рабочей области&gt;. Подписку &lt;ИД подписки&gt; не зарегистрировано для использования Microsoft.Insights). Чтобы зарегистрировать учетную запись, а затем повторить процедуру, следуйте указаниям из статьи [Устранение неполадок Диагностики Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

    Если вы хотите изменить способ сохранения журналов диагностики в дальнейшем, вернитесь на эту страницу, чтобы изменить параметры журнала диагностики для своей учетной записи.

## <a name="turn-on-logging-by-using-azure-cli"></a>Включите ведение журнала с помощью Azure CLI

Чтобы включить метрики и журналы диагностики с помощью Azure CLI, используйте следующие команды:

- Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения.

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` — это имя учетной записи Azure Cosmos DB. Ресурс имеет формат "/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/провидерс/Микрософт.документдб/датабасеаккаунтс/ `storage-account` < Azure_Cosmos_account_name >" — имя учетной записи хранения, в которую вы необходимо отправить журналы. Можно записать в журнал другие журналы, обновив значения параметров категории на "MongoRequests" или "DataPlaneRequests". 

- Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` — это имя учетной записи Azure Cosmos DB. `event-hub-rule` — Это идентификатор правила концентратора событий. 

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

## <a name="turn-on-logging-by-using-powershell"></a>Включение ведения журнала с помощью PowerShell

Чтобы включить журнал ведения диагностики с помощью PowerShell, вам понадобится Azure PowerShell версии 1.0.1 или выше.

Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

Если инструмент Azure PowerShell уже установлен, но вы не знаете его версию, введите `(Get-Module azure -ListAvailable).Version` в консоли PowerShell.  

### <a id="connect"></a>Подключение к подпискам
Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

```powershell
Connect-AzAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записью, и по умолчанию использует первую из них.

Если у вас есть несколько подписок, возможно, вам придется указать подписку, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzSubscription
```

Затем укажите подписку, связанную с учетной записью Azure Cosmos DB, данные которой будут регистрироваться. Для этого введите следующую команду:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Если с учетной записью связано несколько подписок, очень важно указать подписку, которую нужно использовать.
>
>

Дополнительные сведения о настройке Azure PowerShell см. в статье [Общие сведения об Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Создание учетной записи хранения для журналов
Хотя вы можете использовать и имеющуюся учетную запись хранения для журналов, в этом руководстве мы создадим учетную запись хранения, которая будет использоваться для журналов Azure Cosmos DB. Для удобства мы сохраним сведения об учетной записи хранения в переменной с именем **sa**.

Чтобы упростить управление, в этом руководстве мы используем ту же группу ресурсов, которая содержит базу данных Azure Cosmos. При необходимости замените значения **ContosoResourceGroup**, **contosocosmosdblogs** и **Центрально-северная часть США** своими:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Если вы решили использовать существующую учетную запись хранения, она должна использовать ту же подписку, что и Azure Cosmos DB. Учетная запись также должна использовать модель развертывания с помощью Resource Manager, а не классическую модель развертывания.
>
>

### <a id="identify"></a>Определение учетной записи Azure Cosmos DB для журналов
Задайте в качестве имени учетной записи Azure Cosmos DB переменную **account**, где **ResourceName** — это имя учетной записи Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Включение ведения журналов
Чтобы включить ведение журнала для Azure Cosmos DB, используйте командлет `Set-AzDiagnosticSetting` с переменными для новой учетной записи хранения, учетной записью хранения Azure Cosmos DB и категорией, для которой нужно включить ведение журнала. Выполните следующую команду, установив для флага **-Enabled** значение **$true**.

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Выходные данные команды должны выглядеть следующим образом:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Выходные данные команды подтверждают, что ведение журнала включено для базы данных и сведения сохраняются в учетной записи хранения.

При необходимости можно также задать политику хранения для журналов, например политику, в соответствии с которой старые журналы автоматически удаляются. Например, задайте политику хранения, установив для флага **-RetentionEnabled** значение **$true**, а для параметра **-RetentionInDays** значение **90**, чтобы автоматически удалять журналы, которые хранятся более 90 дней.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Доступ к журналам
Журналы Azure Cosmos DB для категории **DataPlaneRequests** хранятся в контейнере **Insights-Logs-DataPlaneRequests** в указанной учетной записи хранения. 

Сначала создайте переменную для имени контейнера. Эта переменная будет использоваться по всему пошаговому руководству.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Чтобы получить список всех больших двоичных объектов в этом контейнере, введите следующее:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Выходные данные команды должны выглядеть следующим образом:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Как видно из этих выходных данных, имена больших двоичных объектов соответствуют соглашению об именовании: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Для значений даты и времени используется время в формате UTC.

Так как ту же учетную запись можно использовать при сборе журналов для нескольких ресурсов, вы можете использовать полный идентификатор ресурса в имени большого двоичного объекта для получения доступа к конкретным большим двоичным объектами и их загрузки. Перед тем как это сделать, мы рассмотрим загрузку всех больших двоичных объектов.

Во-первых, создайте папку для загрузки BLOB-объектов. Пример:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Затем выведите список всех больших двоичных объектов.  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Чтобы загрузить большие двоичные объекты в папку назначения, передайте этот список с помощью команды `Get-AzStorageBlobContent`:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

При выполнении второй команды разделитель **/** в именах больших двоичных объектов используется для создания полной структуры папки в конечной папке. Эта структура папок будет использоваться для скачивания и хранения больших двоичных объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Пример:

* Если у вас есть несколько баз данных и вы хотите скачать журналы только для одной базы данных с именем **CONTOSOCOSMOSDB3**, используйте команду:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Если у вас есть несколько групп ресурсов и вы хотите загрузить журналы только для одной из них, используйте команду `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`.

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Если вы хотите загрузить все журналы за июль 2017 г., используйте команду `-Blob '*/year=2017/m=07/*'`.

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Вы также можете выполнить следующие команды:

* Чтобы запросить сведения о состоянии параметров диагностики для ресурса базы данных, выполните команду `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Чтобы отключить ведение журнала категории **DataPlaneRequests** для ресурса учетной записи базы данных, выполните команду `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Большие двоичные объекты, возвращаемые в каждом из этих запросов, хранятся как текст в формате JSON, как показано в следующем коде.

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Дополнительные сведения о данных в каждом большом двоичном объекте JSON см. в разделе [Интерпретация журналов Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Управление журналами

Журналы диагностики становятся доступными в вашей учетной записи через два часа с момента, когда была выполнена операция Azure Cosmos DB. Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов и ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.
* Срок хранения запросов для плоскости данных, которые архивированы в учетной записи хранения, задается на портале при выборе параметра **Log DataPlaneRequests** (Журнал DataPlaneRequests). Чтобы изменить эту настройку, ознакомьтесь с разделом [Включение ведения журнала на портале Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Просмотр журналов в Azure Monitor

Если вы выбрали параметр **отправить log Analytics** при включении ведения журнала диагностики, диагностические данные из контейнера перенаправляются в Azure Monitor журналы в течение двух часов. При просмотре Azure Monitor журналов сразу же после включения ведения журнала вы не увидите никаких данных. Подождите два часа и повторите попытку. 

Перед просмотром журналов проверьте, обновлена ли Рабочая область Log Analytics для использования нового языка запросов Kusto. Чтобы проверить, откройте [портал Azure](https://portal.azure.com), выберите **log Analytics рабочие области** слева, а затем выберите имя рабочей области, как показано на следующем рисунке. Отобразится страница **Рабочая область Log Analytics**.

![Azure Monitor журналов в портал Azure](./media/logging/azure-portal.png)

>[!NOTE]
>Рабочие области OMS теперь называются рабочими областями Log Analytics.  

Если на странице **Рабочая область Log Analytics** отображается указанное ниже сообщение, это значит, что рабочая область не обновлена и не может использовать новый язык. Дополнительные сведения об обновлении для использования нового языка запросов см. в статье об [обновлении рабочей области Azure Log Analytics до нового поиска по журналам](../log-analytics/log-analytics-log-search-upgrade.md). 

![Сообщение об обновлении журналов Azure Monitor](./media/logging/upgrade-notification.png)

Чтобы просмотреть данные диагностики в журналах Azure Monitor, откройте страницу **поиска** по журналам в меню слева или в области **управления** страницы, как показано на следующем рисунке:

![Параметры поиска по журналам на портале Azure](./media/logging/log-analytics-open-log-search.png)

Теперь, когда сбор данных включен, выполните следующий пример поиска по журналам, используя новый язык запросов, чтобы увидеть 10 последних журналов: `AzureDiagnostics | take 10`.

![Пример поиска 10 самых последних журналов](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Запросы

Ниже приведены некоторые дополнительные запросы, которые можно ввести в поле **поиска** по журналам, чтобы упростить мониторинг контейнеров Azure Cosmos. Эти запросы поддерживают [новый язык](../log-analytics/log-analytics-log-search-upgrade.md). 

Дополнительные сведения о значении данных, возвращаемых каждым поиском по журналам, см. в разделе [Интерпретация журналов](#interpret).

* Для запроса всех журналов диагностики из Azure Cosmos DB за указанный период времени:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Для запроса 10 последних событий журнала:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Для запроса всех операций, сгруппированных по типу операции:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Для запроса всех операций, сгруппированных по **ресурсу**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Для запроса всех действий пользователя, сгруппированных по ресурсу:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Это команда для журнала действий, а не для журнала диагностики.

* Для запроса операций, которые выполнялись дольше 3 миллисекунд:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Для запроса агента, выполняющего операции:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Для запроса времени выполнения длительных операций:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Дополнительные сведения об использовании нового языка поиска по журналам см. в разделе [сведения о поиске по журналам в Azure Monitor журналах](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Интерпретация журналов

Диагностические данные, хранящиеся в службе хранилища Azure и в журналах Azure Monitor, используют аналогичную схему. 

В следующей таблице описано содержимое каждой записи журнала.

| Поле или свойство службы хранилища Azure | Свойство журналов Azure Monitor | Описание |
| --- | --- | --- |
| **time** | **TimeGenerated** | Дата и время (UTC) выполнения операции. |
| **resourceId** | **Ресурс** | Учетная запись Azure Cosmos DB, для которой включены журналы.|
| **category** | **Категория** | Для журналов Azure Cosmos DB единственным доступным значением является **DataPlaneRequests**. |
| **operationName** | **OperationName** | Имя операции. В качестве значения можно использовать любую из следующих операций: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed или Upsert.   |
| **properties** | Н/Д | Содержимое этого поля описано в строках, приведенных ниже. |
| **activityId** | **activityId_g** | Уникальный идентификатор GUID регистрируемой операции. |
| **userAgent** | **userAgent_s** | Строка, которая указывает агент пользователя клиента, выполняющий запрос. Формат: {имя_агента_пользователя} / {версия}.|
| **рекуестресаурцетипе** | **requestResourceType_s** | Тип доступного ресурса. В качестве значения можно использовать любой из следующих типов ресурсов: Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction или Offer. |
| **statusCode** | **statusCode_s** | Состояние ответа операции. |
| **requestResourceId** | **ResourceId** | Идентификатор ресурса, который относится к запросу. В зависимости от выполняемой операции значение может указывать на databaseRid, collectionRid или documentRid.|
| **clientIpAddress** | **clientIpAddress_s** | IP-адрес клиента. |
| **requestCharge** | **requestCharge_s** | Количество ЕЗ, которые используются при операции. |
| **collectionRid** | **collectionId_s** | Уникальный идентификатор коллекции.|
| **duration** | **duration_s** | Длительность операции в тактах. |
| **requestLength** | **requestLength_s** | Длина запроса в байтах. |
| **responseLength** | **responseLength_s** | Длина ответа в байтах.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Это свойство должно быть заполнено, если [маркеры ресурсов](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) используются для проверки подлинности. Значение указывает на идентификатор ресурса пользователя. |

## <a name="next-steps"></a>Следующие шаги

- Чтобы понять, как включать ведение журнала, и узнать, какие метрики и категории журналов поддерживаются различными службами Azure, ознакомьтесь со статьями [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) и [Сбор и использование данных журнала из ресурсов Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
- Прочтите эти статьи, чтобы узнать о концентраторах событий:
   - [Что такое Центры событий Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Начало работы с Центрами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Ознакомьтесь с разделом [скачивании метрик и журналов диагностики из службы хранилища Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Прочитайте [сведения о поиске по журналам в Azure Monitor журналах](../log-analytics/log-analytics-log-search-new.md).

---
title: Доставка событий с использованием управляемого удостоверения службы
description: В этой статье описывается, как включить управляемое удостоверение службы для раздела сетки событий Azure. Используйте его для переадресации событий в поддерживаемые назначения.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700656"
---
# <a name="event-delivery-with-managed-identity"></a>Доставка событий с использованием управляемого удостоверения
В этой статье описано, как включить [управляемое удостоверение службы](../active-directory/managed-identities-azure-resources/overview.md) для раздела сетки событий или домена. Используйте его для переадресации событий в поддерживаемые назначения, такие как очереди и разделы служебной шины, центры событий и учетные записи службы хранилища.

Ниже описаны действия, которые подробно описаны в этой статье:
1. Создание раздела или домена с назначенным системой удостоверением (или) обновление существующего раздела или домена для включения удостоверения. 
2. Добавьте удостоверение к соответствующей роли (например, "отправитель данных служебной шины") в назначении (например, "очередь служебной шины").
3. При создании подписок на события включите использование удостоверения для доставки событий в назначение. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Создание раздела или домена с удостоверением
Для начала рассмотрим, как создается раздел или домен с удостоверением, управляемым системой.

### <a name="using-azure-portal"></a>Использование портала Azure
Можно включить назначенное системой удостоверение для раздела или домена в момент его создания на портале Azure. На следующем рисунке показано, как включить управляемое системой удостоверение для раздела. Фактически можно выбрать параметр **Включить назначенное системой удостоверение** на странице **Дополнительно** мастера создания разделов. Этот параметр также отображается на странице **Дополнительно** мастера создания домена. 

![Включение удостоверения при создании раздела](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Использование Azure CLI
Можно также использовать Azure CLI для создания раздела или домена с удостоверением, назначенным системой. Используйте команду `az eventgrid topic create` с параметром `--identity`, для которого задано значение `systemassigned`. Если не указать значение для этого параметра, используется значение по умолчанию `noidentity`. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Аналогичным образом можно использовать команду `az eventgrid domain create`, чтобы создать домен с удостоверением, управляемым системой.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Включение удостоверения для существующего раздела или домена
В последнем разделе мы рассказали о том, как включить управляемое системой удостоверение при создании нового раздела или домена. Из этого раздела вы узнаете, как включить управляемое системой удостоверение для существующего раздела или домена. 

### <a name="using-azure-portal"></a>Использование портала Azure
1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выполните поиск **разделов сетки событий** в строке поиска.
3. Выберите **раздел**, для которого необходимо включить управляемое удостоверение. 
4. Перейдите на вкладку **Удостоверение**. 
5. Активируйте параметр, чтобы включить удостоверение. 

    Аналогичные действия можно выполнить для включения удостоверения для домена сетки событий.

### <a name="using-azure-cli"></a>Использование Azure CLI
Используйте команду `az eventgrid topic update` с параметром `--identity`, для которого установлено значение `systemassigned`, чтобы включить назначенное системой удостоверение для существующего раздела. Чтобы отключить удостоверение, укажите значение `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Для обновления существующего домена используется аналогичная команда (`az eventgrid domain update`).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Поддерживаемые назначения и роли контроля доступа на основе ролей (RBAC)
После включения удостоверения для раздела или домена сетки событий Azure автоматически создает удостоверение в Azure Active Directory (Azure AD). Это удостоверение добавляется в соответствующие роли RBAC, чтобы раздел или домен мог пересылать события в поддерживаемые назначения. Например, удостоверение можно добавить в роль отправителя **Отправитель данных центров событий Azure** для пространства имен центров событий, чтобы раздел сетки событий мог пересылать события в центры событий в этом пространстве имен.  

В настоящее время сетка событий Azure поддерживает разделы или домены, настроенные с использованием управляемого системой удостоверения, что позволяет переадресовывать события в следующие назначения. Эта таблица также предоставляет роли, в которых должно быть удостоверение, чтобы раздел мог переадресовывать события.

| Назначение | Роль RBAC | 
| ----------- | --------- | 
| Очереди и разделы служебной шины Azure | [Отправитель данных служебной шины Azure](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| концентратор событий; | [Отправитель данных центров событий Azure](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Хранилище BLOB-объектов | [участник данных BLOB-объектов хранилища](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues); |
| Хранилище очередей |[Отправитель сообщений данных в очередь хранилища](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Добавление удостоверения в роли RBAC в различных назначениях
В этом разделе описывается добавление удостоверения для раздела или домена в роль RBAC. 

### <a name="using-azure-portal"></a>Использование портала Azure
Можно использовать **портал Azure**, чтобы назначить удостоверение раздела/домена для соответствующей роли, чтобы раздел/домен мог переадресовывать события в назначение. 

В следующем примере показано добавление управляемого удостоверения для раздела сетки событий с именем **msitesttopic** в роль **Отправитель данных служебной шины Azure** для служебной шины **namespace**, которая содержит ресурс очереди или раздела. При добавлении в роль на уровне пространства имен раздел может переадресовывать события во все сущности, содержащиеся в пространстве имен. 

1. Перейдите к **пространству имен служебной шины** на [портале Azure](https://portal.azure.com). 
2. На панели слева выберите **Управление доступом**. 
3. В разделе **Добавление назначения роли** выберите **Добавить**. 
4. На странице **Добавление назначения роли** выполните следующие действия.
    1. Выберите роль. В этом случае используется роль **Отправитель данных служебной шины Azure**. 
    2. Выберите **удостоверение** для раздела или домена. 
    3. Выберите **Сохранить**, чтобы сохранить конфигурацию.

Эти действие аналогично добавлению удостоверения для других ролей, упомянутых в таблице. 

### <a name="using-azure-cli"></a>Использование Azure CLI
В примере в этом разделе показано, как использовать **Azure CLI** для добавления удостоверения в роль RBAC. Примеры команд относятся к разделам сетки событий. Для доменов сетки событий используются похожие команды. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Получение идентификатора субъекта для удостоверения системы раздела 
Сначала необходимо получить идентификатор субъекта управляемого системой удостоверения и назначить удостоверение соответствующим ролям.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Создание назначения роли для центров событий в различных областях 
В следующем примере интерфейса командной строки показано, как добавить удостоверение раздела в роль **Отправитель данных концентратора событий Azure** на уровне пространства имен или на уровне центра событий. При создании назначения роли в пространстве имен раздел может переадресовывать события во все центры событий в этом пространстве имен. При создании на уровне центра событий раздел может пересылать события только в этот конкретный центр событий. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Создание назначения роли для раздела служебной шины в различных областях 
В следующем примере интерфейса командной строки показано, как добавить удостоверение раздела в роль **Отправитель данных служебной шины Azure** на уровне пространства имен или на уровне раздела служебной шины. При создании назначения роли в пространстве имен раздел сетки событий может пересылать события во все сущности (очереди или разделы служебной шины) в пределах этого пространства имен. При создании на уровне очереди или раздела служебной шины раздел сетки событий может пересылать события только в эту конкретную очередь или раздел служебной шины. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Создание подписок на события, использующие удостоверение
После того как раздел или домен с удостоверением, управляемым системой, добавит удостоверение для соответствующей роли в назначении, можно приступать к созданию подписок, использующих это удостоверение. 

### <a name="using-azure-portal"></a>Использование портала Azure
При создании подписки на события отображается параметр, позволяющий включить использование назначенного системой удостоверения для конечной точки в разделе **СВЕДЕНИЯ О КОНЕЧНОЙ ТОЧКЕ**. 

![Включение удостоверения при создании подписки на события для очереди служебной шины](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Можно также включить использование назначенного системой удостоверения для недоставленных сообщений на вкладке **Дополнительные функции**. 

![Использование назначаемого системой удостоверения для недоставленных сообщений](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Использование Azure CLI. Очередь служебной шины 
В этом разделе вы узнаете, как с помощью **Azure CLI** разрешить использование назначенного системой удостоверения для доставки событий в очередь служебной шины. Удостоверение должно быть членом роли **Отправитель данных служебной шины Azure**. Оно также должно быть членом роли **Участник данных BLOB-объектов хранилища** в учетной записи службы хранилища, используемой для недоставленных сообщений. 

#### <a name="define-variables"></a>Определение переменных
Сначала укажите значения для следующих переменных, которые будут использоваться в команде CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>Создайте подписку на события, используя управляемое удостоверение для доставки 
Эта команда создает подписку на событие для раздела сетки событий с типом конечной точки **Очередь служебной шины**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>Создание подписки на события с использованием управляемого удостоверения для доставки и недоставленных сообщений
Эта команда создает подписку на событие для раздела сетки событий с типом конечной точки **Очередь служебной шины**. Она также указывает, что управляемое системой удостоверение используется для недоставленных сообщений. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI. Центры событий 
В этом разделе вы узнаете, как с помощью **Azure CLI** разрешить использование назначенного системой удостоверения для доставки событий в центр событий. Удостоверение должно быть членом роли **Отправитель данных центров событий Azure**. Оно также должно быть членом роли **Участник данных BLOB-объектов хранилища** в учетной записи службы хранилища, используемой для недоставленных сообщений. 

#### <a name="define-variables"></a>Определение переменных
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Создайте подписку на события, используя управляемое удостоверение для доставки 
Эта команда создает подписку на событие для раздела сетки событий с типом конечной точки **Центры событий**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Создайте подписку на события, используя управляемое удостоверение для доставки и недоставленных сообщений 
Эта команда создает подписку на событие для раздела сетки событий с типом конечной точки **Центры событий**. Она также указывает, что управляемое системой удостоверение используется для недоставленных сообщений. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI. Очередь службы хранилища Azure 
В этом разделе вы узнаете, как с помощью **Azure CLI** разрешить использование назначенного системой удостоверения для доставки событий в очередь службы хранилища Azure. Удостоверение должно быть членом роли **Участник данных BLOB-объектов хранилища**.

#### <a name="define-variables"></a>Определение переменных  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Создайте подписку на события, используя управляемое удостоверение для доставки 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Создайте подписку на события, используя управляемое удостоверение для доставки и недоставленных сообщений 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об управляемых удостоверениях службы см. в статье [Управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md). 
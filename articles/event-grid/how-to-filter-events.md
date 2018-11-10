---
title: Фильтрация событий для Сетки событий Azure
description: Создание подписки на службу "Сетка событий Azure" для фильтрации событий.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252046"
---
# <a name="filter-events-for-event-grid"></a>Фильтрация событий для Сетки событий

В этой статье показано, как фильтровать события при создании подписки на Сетку событий. Дополнительные сведения о параметрах фильтрации событий см. в статье [Understand event filtering for Event Grid subscriptions](event-filtering.md) (Общие сведения о фильтрации событий для подписок на Сетку событий).

## <a name="filter-by-event-type"></a>Фильтрация по типу события

При создании подписки на Сетку событий можно указать, какие [типы событий](event-schema.md) будут отправляться в конечную точку. В приведенных в этом разделе примерах создаются подписки на события для группы ресурсов, но в `Microsoft.Resources.ResourceWriteFailure` и `Microsoft.Resources.ResourceWriteSuccess` отправляется ограниченное количество событий. Если вам нужно больше возможностей фильтрации событий по типу, см. сведения о [фильтрации по дополнительным операторам и полям данных](#filter-by-advanced-operators-and-data-fields).

В PowerShell при создании подписки используйте параметр `-IncludedEventType`.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

В Azure CLI используйте параметр `--included-event-types`. В приведенном ниже примере используется Azure CLI в оболочке bash.

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

В шаблоне Resource Manager используйте свойство `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Фильтрация по теме

Вы можете фильтровать события по теме данных. Можно указать значение, которое сопоставляется с началом или концом темы. Если вам нужно больше возможностей фильтрации событий по теме, см. сведения о [фильтрации по дополнительным операторам и полям данных](#filter-by-advanced-operators-and-data-fields).

В приведенном ниже примере PowerShell создается подписка, которая позволяет фильтровать события по началу темы. Чтобы ограничить события, предназначенные для конкретного ресурса, используйте параметр `-SubjectBeginsWith`. Можно передать идентификатор ресурса группы безопасности сети.

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

В следующем примере PowerShell создается подписка для хранилища BLOB-объектов. Она ограничивает события темой, которая заканчивается на `.jpg`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

В приведенном ниже примере Azure CLI создается подписка, которая позволяет фильтровать события по началу темы. Чтобы ограничить события, предназначенные для конкретного ресурса, используйте параметр `--subject-begins-with`. Можно передать идентификатор ресурса группы безопасности сети.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

В следующем примере Azure CLI создается подписка для хранилища BLOB-объектов. Она ограничивает события темой, которая заканчивается на `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

В приведенном ниже примере шаблона Resource Manager создается подписка, которая позволяет фильтровать события по началу темы. Чтобы ограничить события, предназначенные для конкретного ресурса, используйте свойство `subjectBeginsWith`. Можно передать идентификатор ресурса группы безопасности сети.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

В следующем примере шаблона Resource Manager создается подписка для хранилища BLOB-объектов. Она ограничивает события темой, которая заканчивается на `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Фильтрация по операторам и данным

Чтобы пользоваться расширенной фильтрацией, нужно установить расширение предварительной версии для Azure CLI. Вы можете использовать [CloudShell](/azure/cloud-shell/quickstart) или установить Azure CLI локально.

### <a name="install-extension"></a>Установка расширения

В CloudShell:

* Если вы установили расширение ранее, обновите его `az extension update -n eventgrid`.
* Если расширение еще не установлено, установите его `az extension add -n eventgrid`.

При локальной установке:

1. Удалите Azure CLI локально.
1. Установите [последнюю версию](/cli/azure/install-azure-cli) Azure CLI.
1. Запустите командное окно.
1. Удалите предыдущие версии расширения `az extension remove -n eventgrid`.
1. Установите расширение `az extension add -n eventgrid`.

Теперь вы можете использовать расширенную фильтрацию.

### <a name="subscribe-with-advanced-filters"></a>Подписка с дополнительными фильтрами

Дополнительные сведения об операторах и ключах для расширенной фильтрации см. в разделе [Advanced filtering](event-filtering.md#advanced-filtering) (Расширенная фильтрация).

В приведенном ниже примере создается настраиваемый раздел. Формируется подписка и выполняется фильтрация по значению в объекте данных. События со свойством, выделенным синим, красным или зеленым цветом, отправляются в подписку.

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "2018-11-30"
```

Обратите внимание на дату окончания срока действия подписки. После этой даты подписка автоматически перестанет действовать. Задавайте срок действия только для подписок, которые нужны в течение ограниченного времени.

### <a name="test-filter"></a>Тестирование фильтрации

Чтобы протестировать фильтрацию, отправьте событие, выделенное зеленым цветом.

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Оно отправится в конечную точку.

Чтобы протестировать сценарий, когда событие не отправлено, отправьте событие, выделенное желтым цветом.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Среди допустимых значений подписки желтый цвет не указан, поэтому событие не будет отправлено.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Дополнительные сведения о ключе аутентификации см. в статье [Сетка событий: безопасность и проверка подлинности](security-authentication.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).

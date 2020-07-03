---
title: Функции шаблонов — ресурсы
description: Описывает функции, используемые в шаблоне Azure Resource Manager для получения значений ресурсов.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 508933cbea3e21fdec63907cef73102866732bb1
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891008"
---
# <a name="resource-functions-for-arm-templates"></a>Функции ресурсов для шаблонов ARM

Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов в шаблоне Azure Resource Manager (ARM):

* [екстенсионресаурцеид](#extensionresourceid)
* [числ](#list)
* [поставщик](#providers)
* [IsReference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [подписчик](#subscription)
* [субскриптионресаурцеид](#subscriptionresourceid)
* [тенантресаурцеид](#tenantresourceid)

Получение значений параметров, переменных или текущего развертывания описано в разделе [Функции для параметров развертывания](template-functions-deployment.md).

## <a name="extensionresourceid"></a>екстенсионресаурцеид

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Возвращает идентификатор ресурса для [ресурса расширения](../management/extension-resource-types.md), который является типом ресурса, который применяется к другому ресурсу для добавления к его возможностям.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| resourceId |Да |строка |Идентификатор ресурса для ресурса, к которому применяется ресурс расширения. |
| тип_ресурса |Да |строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |строка |Имя ресурса. |
| имя_ресурса2 |нет |строка |Сегмент имени следующего ресурса, если это необходимо. |

Продолжайте добавлять имена ресурсов в качестве параметров, если тип ресурса включает больше сегментов.

### <a name="return-value"></a>Возвращаемое значение

Базовый формат идентификатора ресурса, возвращаемого этой функцией:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Сегмент области зависит от расширяемого ресурса.

Когда ресурс расширения применяется к **ресурсу**, идентификатор ресурса возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Когда ресурс расширения будет применен к **группе ресурсов**, формат будет следующим:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Когда к **подписке**применяется ресурс расширения, формат имеет вид:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Если ресурс расширения применяется к **группе управления**, то используется формат:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Пример Екстенсионресаурцеид

В следующем примере возвращается идентификатор ресурса для блокировки группы ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Синтаксис для этой функции зависит от имени из списка операций. Каждая реализация возвращает значения для типа ресурса, который поддерживает операцию list. Имя операции должно начинаться с `list`. Наиболее распространенными вариантами применения являются `listKeys` и `listSecrets`.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |строка |Уникальный идентификатор ресурса. |
| версия_API |Да |строка |Версия API для состояния среды выполнения ресурса. Как правило, указывается в формате **гггг-мм-дд**. |
| functionValues |нет |объект | Объект, содержащий значения для функции. Предоставляйте этот объект только для функций, которые поддерживают прием объекта с параметрами, например **listAccountSas** в учетной записи хранения. В этой статье показан пример передачи значения функции. |

### <a name="valid-uses"></a>Допустимые варианты использования

Функции List можно использовать только в свойствах определения ресурса и в разделе Outputs шаблона или развертывания. При использовании с [итерацией свойства](copy-properties.md)можно использовать функции List для `input` , так как выражение назначается свойству ресурса. Их нельзя использовать с `count` , так как счетчик должен быть определен до разрешения функции List.

### <a name="implementations"></a>Варианты реализации решения

Следующая таблица содержит возможные способы использования list*.

| Тип ресурса | Имя функции |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. Аппконфигуратион/Конфигуратионсторес | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. Блокчейн/Блоккчаинмемберс | [листапикэйс](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. Блокчейн/Блоккчаинмемберс/Трансактионнодес | [листапикэйс](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/реестров/запуски | [листлогсасурл](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/реестра/задачи | [листдетаилс](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/Манажедклустерс/Акцесспрофилес | [листкредентиал](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. Датабокс/задания | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. файл, учетные записи и общие папки | [листсинчронизатионс](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft. шаресубскриптионс/Accounts/ | [листсаурцешаресинчронизатионсеттингс](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. шаресубскриптионс/Accounts/ | [листсинчронизатиондетаилс](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. шаресубскриптионс/Accounts/ | [листсинчронизатионс](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/Иосубкэйс | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/Domains | [listKeys](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/Clusters/databases | [листпринЦипалс](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft. Лабсервицес/пользователи | [листенвиронментс](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. Лабсервицес/пользователи | [листлабс](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/рабочие процессы/запуски/действия | [листекспрессионтрацес](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/рабочие процессы/запуски, действия и повторы | [листекспрессионтрацес](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/рабочие процессы/триггеры | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/рабочие процессы/версии/триггеры | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [листнодес](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/Аппликатионсекуритиграупс | листипконфигуратионс |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft. Полициинсигхтс/исправление | [листдеплойментс](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/Namespaces/Дисастеррековериконфигс/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| Microsoft. Web/апиманажементаккаунтс/API/подключения | листконнектионкэйс |
| Microsoft. Web/апиманажементаккаунтс/API/подключения | listsecrets |
| Microsoft. Web/Sites/Backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft. Web/Sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| Microsoft. Web/Sites/слоты/резервные копии | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft. Web/Sites/слоты/конфигурация | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Чтобы определить, какие типы ресурсов поддерживают операцию list, можно использовать следующие варианты:

* Просмотрите [операции REST API](/rest/api/) для поставщика ресурсов и найдите операции list. Например, в учетных записях хранения есть [операция listKeys](/rest/api/storagerp/storageaccounts).
* Воспользуйтесь командлетом PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). В следующем примере извлекаются все операции list для учетных записей хранения:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Используйте следующую команду Azure CLI, чтобы отфильтровать только операции list:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект зависит от используемой функции списка. Например, listKeys для учетной записи хранения возвращает следующий формат.

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Другие функции list возвращают данные в других форматах. Чтобы просмотреть формат функции, включите ее в раздел outputs, как показано в примере шаблона.

### <a name="remarks"></a>Remarks

Укажите ресурс с помощью его имени или [функции resourceId](#resourceid). Если эта функция списка задана в том же шаблоне, с помощью которого выполняется развертывание ресурса, на который указывает ссылка, следует использовать имя ресурса.

При использовании функции **List** в ресурсе, который условно развернут, функция вычисляется, даже если ресурс не развернут. Если функция **List** ссылается на несуществующий ресурс, возникает ошибка. Используйте функцию **If** , чтобы убедиться, что функция вычисляется только при развертывании ресурса. См. [функцию if](template-functions-logical.md#if) для примера шаблона, который использует if и List с условно развернутым ресурсом.

### <a name="list-example"></a>Пример списка

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) показано, как получить в разделе выходных данных первичный и вторичный ключи из учетной записи хранения. Он также возвращает маркер SAS для учетной записи хранения.

Чтобы получить маркер SAS, передайте объект для времени окончания срока действия. Время окончания срока действия должно быть в будущем. В этом примере показано, как использовать функции перечисления. Как правило, нужно использовать токен SAS в значении ресурса, а не возвращать его в качестве выходного значения. Выходные значения хранятся в истории развертывания и не являются безопасными.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

Возвращает сведения о поставщике ресурсов и поддерживаемых типах ресурсов. Если тип ресурса не указан, функция возвращает все типы, поддерживаемые для поставщика ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| пространство_имен_поставщика |Да |строка |Пространство имен поставщика. |
| тип_ресурса |нет |строка |Тип ресурса в указанном пространстве имен. |

### <a name="return-value"></a>Возвращаемое значение

Все поддерживаемые типы возвращаются в следующем формате:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Упорядочение массива возвращаемых значений не гарантируется.

### <a name="providers-example"></a>Пример поставщиков

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) показано, как использовать функцию provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Для поставщика ресурсов **Microsoft.Web** и типа ресурса **sites** приведенный выше пример возвращает объект в таком формате:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Возвращает объект, представляющий состояние среды выполнения ресурса.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |строка |Имя или уникальный идентификатор ресурса. При указании ссылки на ресурс в текущем шаблоне укажите в качестве параметра только имя ресурса. При ссылке на ранее развернутый ресурс или если имя ресурса неоднозначно, укажите идентификатор ресурса. |
| версия_API |нет |строка |Версия API для указанного ресурса. **Этот параметр является обязательным, если ресурс не подготовлен в рамках того же шаблона.** Как правило, указывается в формате **гггг-мм-дд**. Допустимые версии API для ресурса см. в разделе [Справочник по шаблонам](/azure/templates/). |
| Full |нет |строка |Значение, указывающее, следует ли возвращать полный объект ресурса. Если вы не укажете `'Full'`, возвращается только объект свойств ресурса. Полный объект включает такие значения, как идентификатор ресурса и расположение. |

### <a name="return-value"></a>Возвращаемое значение

Каждый тип ресурса возвращает для функции reference разные свойства. Функция не возвращает данные в едином предварительно заданном формате. Кроме того, возвращаемое значение отличается в зависимости от значения `'Full'` аргумента. Чтобы просмотреть свойства для типа ресурса, возвратите объект в разделе outputs, как показано в примере.

### <a name="remarks"></a>Remarks

Эталонная функция извлекает состояние среды выполнения ранее развернутого ресурса или ресурса, развернутого в текущем шаблоне. В этой статье приведены примеры для обоих сценариев.

Как правило, функция **Reference** используется для возврата определенного значения из объекта, например URI конечной точки большого двоичного объекта или полного доменного имени.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Используйте `'Full'`, если вам нужны значения ресурсов, которые не являются частью схемы свойств. Например, чтобы задать политику доступа к хранилищу ключей, получите свойства идентификатора для виртуальной машины.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Допустимые варианты использования

Эталонную функцию можно использовать только в свойствах определения ресурса и в разделе выходных данных шаблона или развертывания. При использовании с [итерацией свойства](copy-properties.md)можно использовать функцию Reference для `input` , так как выражение назначается свойству ресурса.

Нельзя использовать функцию Reference для задания значения `count` свойства в цикле копирования. Можно использовать для задания других свойств в цикле. Ссылка заблокирована для свойства Count, так как это свойство должно быть определено до разрешения функции Reference.

Нельзя использовать функцию Reference в выходных данных [вложенного шаблона](linked-templates.md#nested-template) для возврата ресурса, развернутого во вложенном шаблоне. Вместо этого используйте [связанный шаблон](linked-templates.md#linked-template).

При использовании функции **Reference** в ресурсе, который условно развернут, функция вычисляется, даже если ресурс не развернут.  Если **Ссылочная** функция ссылается на несуществующий ресурс, возникает ошибка. Используйте функцию **If** , чтобы убедиться, что функция вычисляется только при развертывании ресурса. См. [функцию if](template-functions-logical.md#if) для примера шаблона, который использует оператор If и ссылку с условно развернутым ресурсом.

### <a name="implicit-dependency"></a>Неявная зависимость

С помощью функции reference вы прямо объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне и вы ссылаетесь, используя его имя (а не идентификатор). При этом свойство dependsOn использовать не нужно. Расчет функции выполняется только после развертывания ресурса, на который указывает ссылка.

### <a name="resource-name-or-identifier"></a>Имя или идентификатор ресурса

При ссылке на ресурс, развернутый в том же шаблоне, укажите имя ресурса.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

При ссылке на ресурс, который не развернут в том же шаблоне, укажите идентификатор `apiVersion`ресурса и.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Чтобы избежать неоднозначности в отношении ресурса, на который вы ссылаетесь, можно указать полный идентификатор ресурса.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

При создании полной ссылки на ресурс порядок объединения сегментов из типа и имени представляет собой не только использование этих двух вариантов. Вместо этого после пространства имен используйте пары *типа и имени*, начиная от наименее подходящей к наиболее подходящей.

**{Resource-Provider-Namespace}/{Парент-ресаурце-типе}/{Парент-ресаурце-наме} [/{Чилд-ресаурце-типе}/{Чилд-ресаурце-наме}]**

Пример:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` — правильно, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` — неправильно.

Чтобы упростить создание любого идентификатора ресурса, используйте `resourceId()` функции, описанные в этом документе, а не `concat()` функции.

### <a name="get-managed-identity"></a>Получить управляемое удостоверение

[Управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md) являются [типами ресурсов расширения](../management/extension-resource-types.md) , которые создаются неявно для некоторых ресурсов. Так как управляемое удостоверение не определено явным образом в шаблоне, необходимо указать ссылку на ресурс, к которому применяется удостоверение. Используйте `Full` для получения всех свойств, включая явно созданное удостоверение.

Например, чтобы получить идентификатор клиента для управляемого удостоверения, применяемого к масштабируемому набору виртуальных машин, используйте:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Пример ссылки

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) развертывает ресурс и ссылается на этот ресурс.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

В предыдущем примере возвращаются два объекта. Объект свойств имеет следующий формат:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Полный объект имеет следующий формат:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) ссылается на учетную запись хранения, которая не развертывается в этом шаблоне. Учетная запись хранения уже имеется в той же подписке.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Возвращает объект, который представляет текущую группу ресурсов.

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Свойство **ManagedBy** возвращается только для групп ресурсов, содержащих ресурсы, управляемые другой службой. Для управляемых приложений, кирпичей данных и AKS значение свойства — это идентификатор ресурса для управления ресурсом.

### <a name="remarks"></a>Remarks

Функцию `resourceGroup()` нельзя использовать в шаблоне, который [развернут на уровне подписки](deploy-to-subscription.md). Ее можно использовать только в шаблонах, развернутых в группе ресурсов. `resourceGroup()` Функцию можно использовать в [связанном или вложенном шаблоне (с внутренней областью)](linked-templates.md) , предназначенной для группы ресурсов, даже если родительский шаблон развертывается в подписке. В этом сценарии связанный или вложенный шаблон развертывается на уровне группы ресурсов. Дополнительные сведения о нацеливании на группу ресурсов в развертывании уровня подписки см. в статье [развертывание ресурсов Azure в нескольких подписках или группах ресурсов](cross-resource-group-deployment.md).

Как правило, функция resourceGroup используется для создания ресурсов в одном расположении с группой ресурсов. В следующем примере расположение группы ресурсов используется для значения параметра по умолчанию.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Кроме того, функцию resourceGroup можно использовать для применения тегов из группы ресурсов к ресурсу. Дополнительные сведения см. в статье [применение тегов из группы ресурсов](../management/tag-resources.md#apply-tags-from-resource-group).

При использовании вложенных шаблонов для развертывания в нескольких группах ресурсов можно указать область для вычисления функции resourceGroup. Дополнительные сведения см. в разделе [Развертывание ресурсов Azure в нескольких подписках или группах ресурсов](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Пример группы ресурсов

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) возвращает свойства группы ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

В предыдущем примере возвращается объект в следующем формате:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Возвращает уникальный идентификатор ресурса. Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. Формат возвращаемого идентификатора зависит от того, происходит ли развертывание в области действия группы ресурсов, подписки, группы управления или клиента.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Type | Описание |
|:--- |:--- |:--- |:--- |
| subscriptionId |нет |строка (в формате GUID) |Значение по умолчанию — текущая подписка. Укажите это значение, если нужно получить ресурс из другой подписки. Это значение предоставляется только при развертывании в области действия группы ресурсов или подписки. |
| имя_группы_ресурсов |нет |строка |Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если нужно получить ресурс из другой группы ресурсов. Это значение предоставляется только при развертывании в области группы ресурсов. |
| тип_ресурса |Да |строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |строка |Имя ресурса. |
| имя_ресурса2 |нет |строка |Сегмент имени следующего ресурса, если это необходимо. |

Продолжайте добавлять имена ресурсов в качестве параметров, если тип ресурса включает больше сегментов.

### <a name="return-value"></a>Возвращаемое значение

При развертывании шаблона в области группы ресурсов идентификатор ресурса возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

При использовании в [развертывании на уровне подписки](deploy-to-subscription.md)идентификатор ресурса возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

При использовании в развертывании на [уровне группы управления](deploy-to-management-group.md) или на уровне клиента идентификатор ресурса возвращается в следующем формате:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Чтобы получить идентификатор в других форматах, см.:

* [екстенсионресаурцеид](#extensionresourceid)
* [субскриптионресаурцеид](#subscriptionresourceid)
* [тенантресаурцеид](#tenantresourceid)

### <a name="remarks"></a>Remarks

Количество предоставляемых параметров зависит от того, является ли ресурс родительским или дочерним, а также находится ли ресурс в той же подписке или группе ресурсов.

Чтобы получить идентификатор ресурса для родительского ресурса в той же подписке и группе ресурсов, укажите тип и имя ресурса.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Чтобы получить идентификатор ресурса для дочернего ресурса, обратите внимание на количество сегментов в типе ресурса. Укажите имя ресурса для каждого сегмента типа ресурса. Имя сегмента соответствует ресурсу, существующему для этой части иерархии.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Чтобы получить идентификатор ресурса для ресурса в той же подписке, но в другой группе ресурсов, укажите имя группы ресурсов.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Чтобы получить идентификатор ресурса для ресурса в другой подписке и группе ресурсов, укажите идентификатор подписки и имя группы ресурсов.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Пример идентификатора ресурса

Следующий [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) возвращает идентификатор ресурса для учетной записи хранения в группе ресурсов:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| Название | Тип | Значение |
| ---- | ---- | ----- |
| sameRGOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Строка | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Строка | /subscriptions/{ИД_текущей_подписки}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>Подписка

`subscription()`

Возвращает сведения о подписке для текущего развертывания.

### <a name="return-value"></a>Возвращаемое значение

Функция возвращает значение в следующем формате:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Remarks

При использовании вложенных шаблонов для развертывания в нескольких подписках можно указать область для вычисления функции подписки. Дополнительные сведения см. в разделе [Развертывание ресурсов Azure в нескольких подписках или группах ресурсов](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Пример подписки

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) показана функция subscription, вызываемая в разделе выходных данных.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>субскриптионресаурцеид

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Возвращает уникальный идентификатор ресурса, развернутого на уровне подписки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| subscriptionId |нет |строка (в формате GUID) |Значение по умолчанию — текущая подписка. Укажите это значение, если нужно получить ресурс из другой подписки. |
| тип_ресурса |Да |строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |строка |Имя ресурса. |
| имя_ресурса2 |нет |строка |Сегмент имени следующего ресурса, если это необходимо. |

Продолжайте добавлять имена ресурсов в качестве параметров, если тип ресурса включает больше сегментов.

### <a name="return-value"></a>Возвращаемое значение

Идентификатор возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Remarks

Эта функция используется для получения идентификатора ресурса для ресурсов, которые [развертываются в подписке](deploy-to-subscription.md) , а не в группе ресурсов. Возвращенный идентификатор отличается от значения, возвращаемого функцией [resourceId](#resourceid) , не включая значение группы ресурсов.

### <a name="subscriptionresourceid-example"></a>Пример Субскриптионресаурцеид

Следующий шаблон назначает встроенную роль. Его можно развернуть либо в группе ресурсов, либо в подписке. Для получения идентификатора ресурса для встроенных ролей используется функция Субскриптионресаурцеид.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>тенантресаурцеид

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Возвращает уникальный идентификатор ресурса, развернутого на уровне клиента.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| тип_ресурса |Да |строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |строка |Имя ресурса. |
| имя_ресурса2 |нет |строка |Сегмент имени следующего ресурса, если это необходимо. |

Продолжайте добавлять имена ресурсов в качестве параметров, если тип ресурса включает больше сегментов.

### <a name="return-value"></a>Возвращаемое значение

Идентификатор возвращается в следующем формате:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Remarks

Эта функция используется для получения идентификатора ресурса для ресурса, развернутого в клиенте. Возвращенный идентификатор отличается от значений, возвращаемых другими функциями ИДЕНТИФИКАТОРов ресурсов, не включая значения группы ресурсов или подписки.

## <a name="next-steps"></a>Дальнейшие действия

* Описание разделов в шаблоне Azure Resource Manager см. в разделе [authoring Azure Resource Manager Templates](template-syntax.md).
* Сведения о слиянии нескольких шаблонов см. в разделе [Использование связанных шаблонов с Azure Resource Manager](linked-templates.md).
* Чтобы выполнить итерацию указанного числа раз при создании типа ресурса, см. раздел [Создание нескольких экземпляров ресурсов в Azure Resource Manager](copy-resources.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](deploy-powershell.md).


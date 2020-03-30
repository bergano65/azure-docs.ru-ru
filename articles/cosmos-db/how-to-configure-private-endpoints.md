---
title: Наконудии частной ссылки Azure для учетной записи Azure Cosmos
description: Узнайте, как настроить частную ссылку Azure для доступа к учетной записи Azure Cosmos с помощью частного IP-адреса в виртуальной сети.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 944341b1ef88c7e3d64a74536720eb9fb1d17321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152746"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Наконудии частной ссылки Azure для учетной записи Azure Cosmos

Используя частную ссылку Azure, вы можете подключиться к учетной записи Azure Cosmos через частную конечную точку. Частная конечная точка представляет собой набор частных IP-адресов в подсети в вашей виртуальной сети. Затем можно ограничить доступ к учетной записи Azure Cosmos частными IP-адресами. Когда Private Link сочетается с ограниченными политиками NSG, это помогает снизить риск эксфильтрации данных. Подробнее о частных конечных точках читайте в статье [Azure Private Link.](../private-link/private-link-overview.md)

Private Link позволяет пользователям получить доступ к учетной записи Azure Cosmos из виртуальной сети или из любой виртуальной сети. Ресурсы, отображаемые в Private Link, также доступны на территории по сравнению с частными пирингами через VPN или Azure ExpressRoute. 

Вы можете подключиться к учетной записи Azure Cosmos, настроенной с помощью Private Link, используя метод автоматического или ручного утверждения. Чтобы узнать больше, смотрите раздел [рабочего процесса утверждения](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) документации Private Link. 

В этой статье описаны шаги по созданию частной конечной точки. Предполагается, что вы используете метод автоматического утверждения.

> [!NOTE]
> Частная поддержка конечных точек в настоящее время обычно доступна в поддерживаемых регионах только для режима подключения шлюза. Для прямого режима он доступен в качестве функции предварительного просмотра.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Создание частной конечной точки с помощью портала Azure

Используйте следующие шаги для создания частной конечной точки для существующей учетной записи Azure Cosmos с помощью портала Azure:

1. Из панели **All ресурсов** выберите учетную запись Azure Cosmos.

1. Выберите **приватные соединения конечных точек** из списка настроек, а затем выберите **частную конечную точку:**

   ![Выбор для создания частной конечной точки на портале Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. В **создать частную конечную точку - Основы** панели, введите или выберите следующие детали:

    | Параметр | Значение |
    | ------- | ----- |
    | **Детали проекта** | |
    | Подписка | Выберите свою подписку. |
    | Группа ресурсов | Выберите группу ресурсов.|
    | **Детали инстанции** |  |
    | name | Введите любое имя для вашей частной конечной точки. Если это имя будет принято, создайте уникальное. |
    |Регион| Выберите регион, где требуется развернуть Приватную ссылку. Создайте частную конечную точку в том же месте, где существует ваша виртуальная сеть.|
    |||
1. Выберите **далее: Ресурс**.
1. В окне **Создание частной конечной точки — Ресурс** введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    |Метод подключения  | Выберите **Подключение к ресурсу Azure в моем каталоге.** <br/><br/> Затем вы можете выбрать один из ваших ресурсов для настройки Private Link. Или вы можете подключиться к чужому ресурсу, используя идентификатор ресурса или псевдоним, которым они поделились с вами.|
    | Подписка| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft.AzureCosmosDB/databaseAccounts.** |
    | Ресурс |Выберите учетную запись Azure Cosmos. |
    |Целевой подресурс |Выберите тип API API Azure Cosmos DB, который необходимо сопоставить с ней. Это по умолчанию только один выбор для AIS S'L, MongoDB и Cassandra. Для API Gremlin и Таблицы вы также можете выбрать **Sl,** поскольку эти API совместимы с API S'L. |
    |||

1. Выберите **далее: Конфигурация**.
1. При **создании частной конечной точки - Конфигурация**введите или выберите эту информацию:

    | Параметр | Значение |
    | ------- | ----- |
    |**Сеть**| |
    | Виртуальная сеть| Выберите виртуальную сеть. |
    | Подсеть | Выберите подсеть. |
    |**Частная интеграция DNS**||
    |Интеграция с частной зоной DNS |Нажмите кнопку **Да**. <br><br/> Чтобы подключиться к частной части с вашей частной конечной точкой, вам нужна запись DNS. Мы рекомендуем вам интегрировать вашу частную конечную точку с частной зоной DNS. Вы также можете использовать свои собственные DNS-серверы или создавать DNS-записи, используя файлы-хоста на виртуальных машинах. |
    |Частная зона DNS |Выберите **privatelink.documents.azure.com**. <br><br/> Частная зона DNS определяется автоматически. Вы не можете изменить его с помощью портала Azure.|
    |||

1. Выберите **Review + create** (Просмотреть и создать). На странице **«Обзор» и «Создание»** Azure проверяет вашу конфигурацию.
1. При походе пройденое сообщение **«Проверка»** выберите **«Создать».**

Когда вы одобрили Private Link для учетной записи Azure Cosmos, на портале Azure опция **«Все сети»** в **брандмауэре и панель виртуальных сетей** недоступна.

В следующей таблице показано отображение между различными типами API-типа учетной записи Azure Cosmos, поддерживаемыми подресурсами и соответствующими именами частных зон. Вы также можете получить доступ к учетным записям Gremlin и Table API через API S'L, так что есть две записи для этих API.

|Тип API учетной записи Azure Cosmos  |Поддерживаемые подресурсы (или иноъед группы) |Название частной зоны  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Таблица    |    Таблица     |   privatelink.table.cosmos.azure.com    |
|Таблица     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Получение частных IP-адресов

После того, как будет подготовлена частная конечная точка, можно заказать IP-адреса. Для просмотра IP-адресов с портала Azure:

1. Щелкните **Все ресурсы**.
1. Поиск частной конечной точки, созданной ранее. В этом случае, это **cdbPrivateEndpoint3**.
1. Выберите вкладку **«Обзор»,** чтобы просмотреть настройки DNS и IP-адреса.

![Частные IP-адреса на портале Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Несколько IP-адресов создаются в одной частной конечной точке:

* Одна для глобальной (регион-агностик) конечной точки учетной записи Azure Cosmos
* По одному для каждого региона, где развернута учетная запись Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Создание частной конечной точки с помощью Azure PowerShell

Выполнить следующий скрипт PowerShell для создания частной конечной точки под названием "MyPrivateEndpoint" для существующей учетной записи Azure Cosmos. Замените переменные значения деталями для среды.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Интеграция частной конечной точки с частной зоной DNS

После создания частной конечной точки можно интегрировать ее с частной зоной DNS, используя следующий скрипт PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Получение частных IP-адресов

После подготовки частной конечной точки можно заказать IP-адреса и отображение F-DN, используя следующий скрипт PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Создание частной конечной точки с помощью Azure CLI

Выполнить следующий скрипт Azure CLI для создания частной конечной точки под названием "myPrivateEndpoint" для существующей учетной записи Azure Cosmos. Замените переменные значения деталями для среды.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Интеграция частной конечной точки с частной зоной DNS

После создания частной конечной точки можно интегрировать ее с частной зоной DNS, используя следующий скрипт Azure CLI:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Создание частной конечной точки с помощью шаблона «Менеджер ресурсов»

Вы можете настроить Private Link, создав частную конечную точку в виртуальной сетевой подсети. Этого можно достичь с помощью шаблона управления ресурсами Azure.

Используйте следующий код для создания шаблона менеджера ресурсов под названием "PrivateEndpoint_template.json". Этот шаблон создает частную конечную точку для существующей учетной записи Azure Cosmos S'L API в существующей виртуальной сети.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Определение файла параметров для шаблона**

Создайте файл параметров для шаблона и назовите его "PrivateEndpoint_parameters.json". Добавьте следующий код в файл параметров:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Развертывание шаблона с помощью сценария PowerShell**

Создайте скрипт PowerShell, используя следующий код. Перед запуском скрипта замените идентификатор подписки, имя группы ресурсов и другие переменные значения деталями для среды.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

В скрипте PowerShell `GroupId` переменная может содержать только одно значение. Это значение является типом API учетной записи. Разрешенные значения: `Sql` `MongoDB`, `Cassandra` `Gremlin`, `Table`, и . Некоторые типы учетных записей Azure Cosmos доступны через несколько AAP. Пример:

* Доступ к учетной записи Gremlin API можно получить как с учетных записей Gremlin, так и с S'L API.
* Доступ к учетной записи API таблицы можно получить как из учетных записей Таблицы, так и из API-изЛ.

Для этих учетных записей необходимо создать одну частную конечную точку для каждого типа API. Соответствующий тип API указан `GroupId` в массиве.

После успешного развертывания шаблона можно увидеть выход, аналогичный следующему изображению. Значение— `provisioningState` `Succeeded` если частные конечные точки настроены правильно.

![Выход развертывания для шаблона «Менеджер ресурсов»](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

После развертывания шаблона частные IP-адреса зарезервированы в подсети. Правило брандмауэра учетной записи Azure Cosmos настроено на прием соединений только из частной конечной точки.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Интеграция частной конечной точки с частной зоной DNS

Используйте следующий код для создания шаблона менеджера ресурсов под названием "PrivateZone_template.json". Этот шаблон создает частную зону DNS для существующей учетной записи Azure Cosmos S'L API в существующей виртуальной сети.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Используйте следующий код для создания шаблона менеджера ресурсов под названием "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Определение файла параметров для шаблона**

Создайте следующий файл двух параметров для шаблона. Создайте "PrivateZone_parameters.json". на новый код:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Создайте "PrivateZoneRecords_parameters.json". на новый код:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Развертывание шаблона с помощью сценария PowerShell**

Создайте скрипт PowerShell, используя следующий код. Перед запуском скрипта замените идентификатор подписки, имя группы ресурсов и другие переменные значения деталями для среды.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Настройка пользовательской службы DNS

Вы должны использовать частную зону DNS в подсети, где вы создали частную конечную точку. Наверстримировать конечные точки таким образом, чтобы каждый частный IP-адрес отображался для входа в DNS. (См. свойство `fqdns` в ответе, показанном ранее.)

При создании частной конечной точки можно интегрировать ее с частной зоной DNS в Azure. Если вместо этого вы решите использовать пользовательскую зону DNS, необходимо настроить ее для добавления записей DNS для всех частных IP-адресов, зарезервированных для частной конечных точек.

## <a name="private-link-combined-with-firewall-rules"></a>Частная ссылка в сочетании с правилами брандмауэра

Следующие ситуации и результаты возможны при использовании Private Link в сочетании с правилами брандмауэра:

* Если вы не настраиваете правила брандмауэра, то по умолчанию весь трафик может получить доступ к учетной записи Azure Cosmos.

* Если вы настраиваете общественный трафик или конечную точку службы и создаете частные конечные точки, то различные типы входящего трафика авторизуются соответствующим типом правила брандмауэра.

* Если вы не настраиваете какой-либо конечный поток общественного трафика или службы и создаете частные конечные точки, то учетная запись Azure Cosmos доступна только через частные конечные точки. Если вы не настраиваете общественный трафик или конечную точку обслуживания, после того, как все утвержденные частные конечные точки будут отклонены или удалены, учетная запись открыта для всей сети.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Обновление частной конечной точки при добавлении или удалении региона

Добавление или удаление регионов в учетную запись Azure Cosmos требует добавления или удаления записей DNS для этой учетной записи. После добавления или удаления регионов можно обновить частную dNS-зону подсети, чтобы отразить добавленные или удаленные записи DNS и их соответствующие частные IP-адреса.

Например, представьте, что вы развернете учетную запись Azure Cosmos в трех регионах: «Западный США», «Центральный США» и «Западная Европа». При создании частной конечной точки для вашей учетной записи в подсети зарезервированы четыре частных ИП. Для каждого из трех регионов есть один ИС, и есть один IP для глобальной/агностической конечной точки.

Позже можно добавить новый регион (например, «Восток США») в учетную запись Azure Cosmos. После добавления нового региона необходимо добавить соответствующую запись DNS либо в вашу частную зону DNS, либо к пользовательским DNS.

Вы можете использовать те же действия при удалении области. После удаления региона необходимо удалить соответствующую запись DNS либо из вашей частной зоны DNS, либо из пользовательского DNS.

## <a name="current-limitations"></a>Текущие ограничения

При использовании Private Link с учетной записью Azure Cosmos применяются следующие ограничения:

* Поддержка Private Link для учетных записей и виртуальных сетей Azure Cosmos доступна только в определенных регионах. Список поддерживаемых регионов можно найти в разделе [«Доступные регионы»](../private-link/private-link-overview.md#availability) в статье «Частная ссылка». 

  > [!NOTE]
  > Чтобы создать частную конечную точку, убедитесь, что виртуальная сеть и учетная запись Azure Cosmos находятся в поддерживаемых регионах.

* При использовании Private Link с учетной записью Azure Cosmos с помощью прямого соединения в режиме можно использовать только протокол TCP. Протокол HTTP пока не поддерживается.

* Частная поддержка конечных точек в настоящее время обычно доступна в поддерживаемых регионах только для режима подключения шлюза. Для прямого режима он доступен в качестве функции предварительного просмотра.

* При использовании API-извне Azure Cosmos DB для учетных записей MongoDB поддерживается частная конечная точка для учетных `*.mongo.cosmos.azure.com`записей только в версии сервера 3.6 (т.е. учетные записи, использующие конечную точку в формате). Частная ссылка не поддерживается для учетных записей на сервере версии 3.2 (т.е. учетные записи, использующие конечную точку в формате). `*.documents.azure.com` Чтобы использовать Private Link, следует перенести старые учетные записи в новую версию.

* При использовании API-иного идентимного происшку Azure Cosmos DB для учетных записей MongoDB с частным соединением вы не можете использовать такие инструменты, как Robo 3T, Studio 3T и Mongoose. Конечная точка может иметь поддержку `appName=<account name>` Private Link только в том случае, если указан параметр. Например, `replicaSet=globaldb&appName=mydbaccountname`. Поскольку эти инструменты не передают имя приложения в строке соединения службе, вы не можете использовать Private Link. Но вы все еще можете получить доступ к этим учетным записям с помощью драйверов SDK с 3.6 версии.

* Вы не можете переместить или удалить виртуальную сеть, если она содержит Частную ссылку.

* Вы не можете удалить учетную запись Azure Cosmos, если она прикреплена к частной конечной точке.

* Вы не можете выйти из учетной записи Azure Cosmos в область, которая не отображается на всех частных конечных точках, прилагаемых к учетной записи.

* Сетевому администратору должно быть предоставлено, по крайней мере, разрешение на создание автоматически утвержденных частных конечных точек «К/PrivateEndpointConnectionsApproval» в области учетной записи Azure Cosmos.

### <a name="limitations-to-private-dns-zone-integration"></a>Ограничения на частную интеграцию зон DNS

Записи DNS в частной зоне DNS не удаляются автоматически при удалении частной конечной точки или удалении области из учетной записи Azure Cosmos. Вы должны вручную удалить записи DNS до:

* Добавление новой частной конечный точки, связанной с этой частной зоной DNS.
* Добавление нового региона в любую учетную запись базы данных, которая имеет частные конечные точки, связанные с этой частной зоной DNS.

Если вы не очистите записи DNS, могут возникнуть непредвиденные проблемы плоскости данных. Эти проблемы включают сбой данных в регионах, добавленных после удаления частной конечных точек или удаления региона.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше об особенностях безопасности Azure Cosmos DB, смотрите следующие статьи:

* Для настройки брандмауэра для Azure [Firewall support](firewall-support.md)Cosmos DB см.

* Чтобы узнать, как настроить конечную точку виртуального сетевого сервиса для вашей учетной записи Azure Cosmos, [см.](how-to-configure-vnet-service-endpoint.md)

* Чтобы узнать больше о Private Link, смотрите документацию [Azure Private Link.](../private-link/private-link-overview.md)

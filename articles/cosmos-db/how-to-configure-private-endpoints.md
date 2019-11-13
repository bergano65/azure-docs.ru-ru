---
title: Настройка частной ссылки Azure для учетной записи Azure Cosmos
description: Узнайте, как настроить личную ссылку Azure для доступа к учетной записи Azure Cosmos с помощью частного IP-адреса в виртуальной сети.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007427"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Настройка частной ссылки Azure для учетной записи Azure Cosmos (Предварительная версия)

С помощью функции "Частная связь Azure" можно подключиться к учетной записи Azure Cosmos с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в подсети в виртуальной сети. Затем можно ограничить доступ к учетной записи Azure Cosmos через частные IP-адреса. Если частная ссылка сочетается с ограниченными политиками NSG, она помогает снизить риск утечка данных. Дополнительные сведения о частных конечных точках см. в статье о [частной ссылке Azure](../private-link/private-link-overview.md) .

Частная ссылка позволяет пользователям получить доступ к учетной записи Azure Cosmos из виртуальной сети или из любой одноранговой виртуальной сети. Ресурсы, сопоставленные с частной ссылкой, также доступны локально через частный пиринг через VPN или Azure ExpressRoute. 

Вы можете подключиться к учетной записи Azure Cosmos, настроенной с помощью закрытой ссылки, используя метод автоматического или ручного утверждения. Дополнительные сведения см. в разделе " [Рабочий процесс утверждения](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) " в документации по частному каналу. 

В этой статье описываются действия по созданию частной конечной точки. Предполагается, что вы используете метод автоматического утверждения.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Создайте закрытую конечную точку с помощью портал Azure

Чтобы создать частную конечную точку для существующей учетной записи Azure Cosmos с помощью портал Azure, выполните следующие действия.

1. В области **все ресурсы** выберите учетную запись Azure Cosmos.

1. Выберите **подключения частной конечной точки** в списке параметров, а затем выберите **Частная конечная точка**.

   ![Параметры для создания частной конечной точки в портал Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. В области **Создание частной конечной точки (Предварительная версия) — основы** введите или выберите следующие сведения:

    | Настройка | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | подписку | Выберите свою подписку. |
    | Группа ресурсов | Выберите группу ресурсов.|
    | **Сведения об экземпляре** |  |
    | имя | Введите любое имя для частной конечной точки. Если это имя используется, создайте уникальное. |
    |Регион| Выберите регион, в котором требуется развернуть закрытую ссылку. Создайте частную конечную точку в том же расположении, где находится виртуальная сеть.|
    |||
1. Выберите **Далее: ресурс**.
1. В окне **Создание частной конечной точки — Ресурс** введите или выберите следующую информацию:

    | Настройка | Значение |
    | ------- | ----- |
    |Способ подключения  | Выберите **подключиться к ресурсу Azure в моем каталоге**. <br/><br/> Затем можно выбрать один из ресурсов, чтобы настроить частную ссылку. Вы также можете подключиться к ресурсу другого пользователя с помощью идентификатора ресурса или псевдонима, к которому вам предоставили доступ.|
    | подписку| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft. азурекосмосдб/databaseAccounts**. |
    | Resource (Ресурс) |Выберите учетную запись Azure Cosmos. |
    |Целевой подресурс |Выберите тип API Azure Cosmos DB, который нужно сопоставлять. По умолчанию используется только один вариант для API-интерфейсов SQL, MongoDB и Cassandra. Для API-интерфейсов Gremlin и Table можно также выбрать **SQL** , так как эти API-интерфейсы совместимы с API SQL. |
    |||

1. Выберите **Далее: Конфигурация**.
1. В окне **Создание частной конечной точки (предварительная версия) — Конфигурация** введите или выберите следующую информацию:

    | Настройка | Значение |
    | ------- | ----- |
    |**Сеть**| |
    | виртуальную сеть| Выберите виртуальную сеть. |
    | Подсеть | Выберите подсеть. |
    |**Интеграция Частная зона DNS**||
    |Интеграция с частной зоной DNS |Выберите **Да**. <br><br/> Для частного подключения к частной конечной точке требуется запись DNS. Рекомендуется интегрировать закрытую конечную точку с частной зоной DNS. Вы также можете использовать собственные DNS-серверы или создать записи DNS с помощью файлов узлов на виртуальных машинах. |
    |Частная зона DNS |Выберите **privatelink.Documents.Azure.com**. <br><br/> Частная зона DNS определяется автоматически. Его нельзя изменить с помощью портал Azure.|
    |||

1. Выберите **Review + create** (Просмотреть и создать). На странице " **Проверка и создание** " Azure проверяет конфигурацию.
1. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**.

Если вы утвердили закрытую ссылку для учетной записи Azure Cosmos, в портал Azure, параметр **все сети** в области **брандмауэр и виртуальные сети** недоступен.

В следующей таблице показано сопоставление между различными типами API учетной записи Azure Cosmos, поддерживаемыми подресурсами и соответствующими именами частных зон. Вы также можете получить доступ к учетным записям Gremlin и API таблиц через API SQL, поэтому для этих API-интерфейсов существует две записи.

|Тип API учетной записи Azure Cosmos  |Поддерживаемые подресурсы (или идентификаторы групп) |Имя частной зоны  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|таблица    |    таблица     |   privatelink.table.cosmos.azure.com    |
|таблица     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Получение частных IP-адресов

После подготовки частной конечной точки можно запросить IP-адреса. Чтобы просмотреть IP-адреса из портал Azure:

1. Щелкните **Все ресурсы**.
1. Найдите закрытую конечную точку, созданную ранее. В данном случае это **cdbPrivateEndpoint3**.
1. Перейдите на вкладку **Обзор** , чтобы просмотреть параметры DNS и IP-адреса.

![Частные IP-адреса в портал Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Для каждой частной конечной точки создается несколько IP-адресов:

* Один для глобальной (независимого от региона) конечной точки учетной записи Azure Cosmos.
* По одному для каждого региона, в котором развернута учетная запись Azure Cosmos.

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Создание частной конечной точки с помощью Azure PowerShell

Выполните следующий сценарий PowerShell, чтобы создать частную конечную точку с именем "Миприватиндпоинт" для существующей учетной записи Azure Cosmos. Замените значения переменных на сведения о вашей среде.

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

После создания закрытой конечной точки ее можно интегрировать с частной зоной DNS с помощью следующего сценария PowerShell:

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

После подготовки частной конечной точки можно запросить IP-адреса и сопоставление полного доменного имени, используя следующий сценарий PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Создание частной конечной точки с помощью шаблона диспетчер ресурсов

Вы можете настроить частную связь, создав закрытую конечную точку в подсети виртуальной сети. Это достигается с помощью шаблона Azure Resource Manager. 

Используйте следующий код, чтобы создать шаблон диспетчер ресурсов с именем "PrivateEndpoint_template. JSON". Этот шаблон создает закрытую конечную точку для существующей учетной записи API Azure Cosmos SQL в существующей виртуальной сети.

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

### <a name="define-the-parameters-file-for-the-template"></a>Определение файла параметров для шаблона

Создайте файл параметров для шаблона и назовите его «PrivateEndpoint_parameters. JSON». Добавьте следующий код в файл параметров:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Развертывание шаблона с помощью сценария PowerShell

Создайте скрипт PowerShell с помощью следующего кода. Перед выполнением скрипта замените идентификатор подписки, имя группы ресурсов и другие значения переменных на сведения о вашей среде.

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

В скрипте PowerShell `GroupId` переменная может содержать только одно значение. Это значение является типом API учетной записи. Допустимые значения: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`и `Table`. Некоторые типы учетных записей Azure Cosmos доступны через несколько интерфейсов API. Например,

* Доступ к учетной записи API Gremlin можно получить из учетных записей Gremlin и API SQL.
* Доступ к учетной записи API таблиц можно получить из учетных записей таблиц и API SQL.

Для этих учетных записей необходимо создать одну частную конечную точку для каждого типа API. Соответствующий тип API указывается в массиве `GroupId`.

После успешного развертывания шаблона можно увидеть выходные данные, аналогичные показанным на следующем рисунке. `provisioningState` значение `Succeeded`, если частные конечные точки настроены правильно.

![Выходные данные развертывания для шаблона диспетчер ресурсов](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

После развертывания шаблона частные IP-адреса зарезервированы в подсети. Правило брандмауэра учетной записи Azure Cosmos настроено для приема подключений только от частной конечной точки.

## <a name="configure-custom-dns"></a>Настройка пользовательской службы DNS

Следует использовать частную зону DNS в подсети, в которой была создана частная конечная точка. Настройте конечные точки таким образом, чтобы каждый частный IP-адрес был сопоставлен с записью DNS. (См. сведения о свойстве `fqdns` в ответе, показанном выше.)

При создании частной конечной точки ее можно интегрировать с частной зоной DNS в Azure. Если вместо этого вы решили использовать пользовательскую зону DNS, необходимо настроить ее для добавления записей DNS для всех частных IP-адресов, зарезервированных для частной конечной точки.

## <a name="private-link-combined-with-firewall-rules"></a>Частная ссылка в сочетании с правилами брандмауэра

При использовании частной ссылки в сочетании с правилами брандмауэра возможны следующие ситуации и результаты.

* Если не настроить какие бы то ни было правила брандмауэра, по умолчанию весь трафик будет иметь доступ к учетной записи Azure Cosmos.

* Если вы настраиваете общедоступный трафик или конечную точку службы и создаете частные конечные точки, то соответствующий тип правила брандмауэра разрешается различными типами входящего трафика.

* Если вы не настраиваете общий трафик или конечную точку службы и не создаете частные конечные точки, учетная запись Azure Cosmos доступна только через частные конечные точки. Если не настроить общедоступный трафик или конечную точку службы, то после того, как все утвержденные частные конечные точки будут отклонены или удалены, учетная запись будет открыта для всей сети.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Обновление частной конечной точки при добавлении или удалении региона

Добавление или удаление регионов в учетной записи Azure Cosmos требует добавления или удаления записей DNS для этой учетной записи. Обновите эти изменения соответствующим образом в частной конечной точке, выполнив следующие действия.

1. Когда администратор Azure Cosmos DB добавляет или удаляет регионы, сетевой администратор получает уведомление об ожидающих изменениях. Для частной конечной точки, сопоставленной с учетной записью Azure Cosmos, значение свойства `ActionsRequired` меняется с `None` на `Recreate`. Затем сетевой администратор обновляет закрытую конечную точку, выдавая запрос на размещение с тем же диспетчер ресурсов полезными данными, которые использовались для его создания.

1. После обновления частной конечной точки подсеть можно обновить в частной зоне DNS, отражая добавленные или удаленные записи DNS и соответствующие частные IP-адреса.

Например, предположим, что вы развертываете учетную запись Azure Cosmos в трех регионах: "Западная часть США", "Центральная часть США" и "Западная Европа". При создании частной конечной точки для вашей учетной записи в подсети зарезервированы четыре частных IP-адреса. Существует один IP-адрес для каждого из трех регионов, и существует один IP-адрес для конечной точки, не зависящей от глобальной или региона.

Позже вы можете добавить новый регион (например, "Восточная часть США") в учетную запись Azure Cosmos. По умолчанию новый регион недоступен из существующей частной конечной точки. Администратор учетной записи Azure Cosmos должен обновить подключение к частной конечной точке, прежде чем обращаться к нему из нового региона. 

При выполнении команды ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` выходные данные команды содержат параметр `actionsRequired`. Этот параметр имеет значение `Recreate`. Это значение указывает, что необходимо обновить частную конечную точку. Затем администратор учетной записи Azure Cosmos выполняет команду `Set-AzPrivateEndpoint`, чтобы активировать обновление закрытой конечной точки.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Новый частный IP-адрес автоматически резервируется в подсети в этой частной конечной точке. Значение для `actionsRequired` преобразуется в `None`. Если у вас нет какой-либо частной интеграции с зоной зоне (иными словами, если вы используете пользовательскую частную зону DNS), необходимо настроить частную зону DNS, чтобы добавить новую запись DNS для частного IP-адреса, соответствующего новому региону.

Вы можете использовать те же действия при удалении региона. Частный IP-адрес удаленного региона автоматически освобождается, а флаг `actionsRequired` становится `None`. Если у вас нет частной зоны зоне, необходимо настроить частную зону DNS, чтобы удалить запись DNS для удаленного региона.

Записи DNS в частной зоне DNS не удаляются автоматически при удалении частной конечной точки или удалении региона из учетной записи Azure Cosmos. Необходимо вручную удалить записи DNS.

## <a name="current-limitations"></a>Текущие ограничения

При использовании закрытой ссылки с учетной записью Azure Cosmos действуют следующие ограничения.

* Поддержка закрытых ссылок для учетных записей Azure Cosmos и виртуальных сетей доступна только в конкретных регионах. Список поддерживаемых регионов см. в разделе [Доступные регионы](../private-link/private-link-overview.md#availability) в статье о закрытой ссылке. 

  > [!NOTE]
  > Чтобы создать частную конечную точку, убедитесь, что виртуальная сеть и учетная запись Azure Cosmos находятся в поддерживаемых регионах.

* Если вы используете закрытую связь с учетной записью Azure Cosmos с помощью подключения в прямом режиме, можно использовать только протокол TCP. Протокол HTTP пока не поддерживается.

* Когда вы используете API Azure Cosmos DB для учетных записей MongoDB, Частная конечная точка поддерживается только для учетных записей на сервере версии 3,6 (то есть учетные записи, использующие конечную точку в формате `*.mongo.cosmos.azure.com`). Частная ссылка не поддерживается для учетных записей на сервере версии 3,2 (то есть учетные записи, использующие конечную точку в формате `*.documents.azure.com`). Чтобы использовать частную ссылку, необходимо перенести старые учетные записи в новую версию.

* Если вы используете API Azure Cosmos DB для учетных записей MongoDB с частной ссылкой, вы не можете использовать такие средства, как совместная работа 3T, Studio 3T и Mongoose. Конечная точка может иметь поддержку Private Link, только если указан параметр `appName=<account name>`. Например, `replicaSet=globaldb&appName=mydbaccountname`. Поскольку эти средства не передают имя приложения в строке подключения службе, нельзя использовать частную ссылку. Но вы по-прежнему можете получить доступ к этим учетным записям, используя драйверы пакета SDK с версией 3,6.

* Невозможно переместить или удалить виртуальную сеть, если она содержит закрытую ссылку.

* Вы не можете удалить учетную запись Azure Cosmos, если она подключена к частной конечной точке.

* Вы не можете выполнить отработку отказа учетной записи Azure Cosmos в регион, который не сопоставлен со всеми частными конечными точками, присоединенными к этой учетной записи.

* Чтобы создать автоматически утвержденные частные конечные точки, администратору сети необходимо предоставить по крайней мере разрешение "*/Приватиндпоинтконнектионсаппровал" в области учетной записи Azure Cosmos.

### <a name="limitations-to-private-dns-zone-integration"></a>Ограничения на интеграцию частной зоны DNS

Записи DNS в частной зоне DNS не удаляются автоматически при удалении частной конечной точки или при удалении региона из учетной записи Azure Cosmos. Необходимо вручную удалить записи DNS перед:

* Добавление новой частной конечной точки, связанной с этой частной зоной DNS.
* Добавление нового региона в любую учетную запись базы данных с закрытыми конечными точками, связанными с этой частной зоной DNS.

Если не очистить записи DNS, могут возникнуть непредвиденные проблемы с плоскостью данных. Эти проблемы включают сбой данных в регионах, добавленных после удаления частной конечной точки или удаления региона.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure Cosmos DB средств безопасности см. в следующих статьях:

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в разделе [Поддержка брандмауэра](firewall-support.md).

* Чтобы узнать, как настроить конечную точку службы виртуальной сети для учетной записи Azure Cosmos, см. раздел [Настройка доступа из виртуальных сетей](how-to-configure-vnet-service-endpoint.md).

* Дополнительные сведения о закрытой ссылке см. в документации по [частной ссылке Azure](../private-link/private-link-overview.md) .

---
title: Настройка частной ссылки Azure для учетной записи Azure Cosmos
description: Узнайте, как настроить личную ссылку Azure для доступа к учетной записи Azure Cosmos с помощью частного IP-адреса в виртуальной сети.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 1eb769ec64e50be65d63be43d897c1190789e555
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518766"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Настройка частной ссылки Azure для учетной записи Azure Cosmos (Предварительная версия)

С помощью функции "Частная связь Azure" можно подключиться к учетной записи Azure Cosmos с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в подсети в виртуальной сети. С помощью частной ссылки можно ограничить доступ к заданной учетной записи Cosmos Azure по частным IP-адресам. При совместном использовании с ограниченными NSG политиками частная ссылка помогает снизить риск утечка данных. Дополнительные сведения о частных конечных точках см. в статье о [частной ссылке Azure](../private-link/private-link-overview.md) .

Кроме того, Частная ссылка позволяет получить доступ к учетной записи Cosmos Azure из виртуальной сети или из любой одноранговой виртуальной сети. Ресурсы, сопоставленные с частной ссылкой, также доступны из локальной сети через частный пиринг через VPN или ExpressRoute. 

Вы можете подключиться к учетной записи Azure Cosmos, настроенной с помощью частного канала, используя методы утверждения "автоматически" или "вручную". Дополнительные сведения см. в разделе " [Рабочий процесс утверждения](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) " в документации по частному каналу. В этой статье описываются действия по созданию частной ссылки с учетом использования метода автоматического утверждения.

## <a name="create-a-private-link-using-the-azure-portal"></a>Создание частной ссылки с помощью портал Azure

Чтобы создать частную ссылку для существующей учетной записи Azure Cosmos с помощью портал Azure, выполните следующие действия.

1. В области **все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.

1. Выберите **Подключение к частной конечной точке** в меню Параметры и выберите параметр **Частная конечная точка** .

   ![Создание частной конечной точки с помощью портал Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. В области **Создание частной конечной точки (Предварительная версия) — основные**сведения, введите или выберите следующие данные:

    | Настройка | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Подписка | Выберите свою подписку. |
    | Группа ресурсов | Выберите группу ресурсов.|
    | **Сведения об экземпляре** |  |
    | Имя | Введите любое имя для частной конечной точки. Если это имя используется, создайте уникальное. |
    |Регион| Выберите регион, в котором требуется развернуть закрытую ссылку. Частная конечная точка должна быть создана в том же расположении, где находится виртуальная сеть.|
    |||
1. Выберите **Далее: ресурс**.
1. В окне **Создание частной конечной точки — ресурс**введите или выберите следующие сведения:

    | Настройка | Значение |
    | ------- | ----- |
    |Способ подключения  | Выберите подключиться к ресурсу Azure в моем каталоге. <br/><br/> Этот параметр позволяет выбрать один из ресурсов для настройки частной связи или подключения к ресурсу другого пользователя с ИДЕНТИФИКАТОРом ресурса или псевдонимом, к которому вам предоставили доступ.|
    | Подписка| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft. азурекосмосдб/databaseAccounts**. |
    | Ресурс |Выберите учетную запись Azure Cosmos |
    |Целевой вспомогательный ресурс |Выберите тип API Cosmos DB, который нужно сопоставлять. По умолчанию используется только один вариант для API-интерфейсов SQL, MongoDB и Cassandra. Для API-интерфейсов Gremlin и Table также можно выбрать *SQL* , так как эти API взаимодействуют с API SQL. |
    |||
1. Выберите **Далее: Конфигурация**.
1. В окне **Создание частной конечной точки (Предварительная версия) — конфигурация**введите или выберите следующие сведения:

    | Настройка | Значение |
    | ------- | ----- |
    |**Сеть**| |
    | Виртуальная сеть| Выберите виртуальную сеть. |
    | Подсеть | Выберите подсеть. |
    |**Интеграция Частная зона DNS**||
    |Интеграция с частной зоной DNS |Выберите **Да**. |
    |Зона Частная зона DNS |Выбор *privatelink.Documents.Azure.com* |
    |||

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию.
1. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**.

Если вы утвердили закрытые ссылки для учетной записи Azure Cosmos, в портал Azure параметр **все сети** в области **брандмауэр и виртуальные сети** отображается серым цветом.

### <a name="fetch-the-private-ip-addresses"></a>Получение частных IP-адресов

После подготовки частной конечной точки можно запросить IP-адреса. Для просмотра IP-адресов из портал Azure. Выберите **все ресурсы**, найдите закрытую конечную точку, созданную ранее в этом случае — "dbPrivateEndpoint3", и перейдите на вкладку "Обзор", чтобы просмотреть параметры DNS и IP-адреса:

![Частные IP-адреса в портал Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Для каждой частной конечной точки создается несколько IP-адресов:

* Один для глобальной (независимо от региона) конечной точки учетной записи Azure Cosmos.
* По одному для каждого региона, в котором развернута учетная запись Azure Cosmos.

## <a name="create-a-private-link-using-azure-powershell"></a>Создание частной ссылки с помощью Azure PowerShell

Выполните следующий сценарий Поверсехлл, чтобы создать закрытую ссылку с именем "Миприватиндпоинт" для существующей учетной записи Azure Cosmos. Обязательно замените значения переменных на сведения о конкретной среде.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="fetch-the-private-ip-addresses"></a>Получение частных IP-адресов

После подготовки частной конечной точки можно запросить IP-адреса и сопоставление полных доменных имен, используя следующий сценарий PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Создание частной ссылки с помощью шаблона диспетчер ресурсов

Вы можете настроить частную связь, создав закрытую конечную точку в подсети виртуальной сети. Это достигается с помощью шаблона Azure Resource Manager. Создайте шаблон диспетчер ресурсов с именем "PrivateEndpoint_template. JSON" с помощью следующего кода. Этот шаблон создает закрытую конечную точку для существующей учетной записи API Azure Cosmos SQL в существующей виртуальной сети.

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

### <a name="define-the-template-parameters-file"></a>Определение файла параметров шаблона

Создайте файл параметров для шаблона и назовите его "PrivateEndpoint_parameters. JSON". Добавьте следующий код в файл параметров:

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

Затем создайте сценарий PowerShell со следующим кодом. Перед запуском скрипта обязательно Замените идентификатор подписки, имя группы ресурсов и другие значения переменных на сведения о конкретной среде.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

В скрипте PowerShell переменная "GroupId" может содержать только одно значение, которое является типом API учетной записи. Допустимые значения: SQL, MongoDB, Cassandra, Gremlin и Table. Некоторые типы учетных записей Azure Cosmos доступны через несколько интерфейсов API. Например:

* Доступ к учетной записи API Gremlin можно получить из учетных записей Gremlin и API SQL.
* Доступ к учетной записи API таблиц можно получить из учетных записей таблиц и API SQL.

Для таких учетных записей необходимо создать одну частную конечную точку для каждого типа API с соответствующим типом API, указанным в массиве "GroupId".

После успешного развертывания шаблона можно увидеть выходные данные, аналогичные показанным на следующем рисунке. Если частные конечные точки настроены правильно, значение provisioningState — "успешно".

![Выходные данные развертывания шаблона диспетчер ресурсов](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

После развертывания шаблона частные IP-адреса зарезервированы в подсети. Правило брандмауэра учетной записи Azure Cosmos настроено для приема подключений только от частной конечной точки.

## <a name="configure-private-dns"></a>Настройка частных DNS-серверов

Во время предварительной версии частной ссылки следует использовать частный DNS в подсети, в которой создана частная конечная точка. И настройте конечные точки таким образом, чтобы каждый частный IP-адрес был сопоставлен с записью DNS (см. свойство "FQDN" в ответе, показанном выше).

## <a name="firewall-configuration-with-private-link"></a>Конфигурация брандмауэра с закрытой ссылкой

Ниже приведены различные ситуации и результаты, связанные с использованием частной ссылки в сочетании с правилами брандмауэра.

* Если правила брандмауэра не настроены, по умолчанию учетная запись Azure Cosmos доступна для всего трафика.

* Если настроен общедоступный сетевой трафик или конечная точка службы и созданы частные конечные точки, то соответствующий тип правила брандмауэра разрешается различным типам входящего трафика.

* Если общий трафик или конечная точка службы не настроены и созданы частные конечные точки, учетная запись Azure Cosmos доступна только через частные конечные точки.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Обновление частной конечной точки при добавлении или удалении региона

Добавление или удаление регионов в учетной записи Azure Cosmos требует добавления или удаления записей DNS для этой учетной записи. Эти изменения должны быть соответствующим образом обновлены в частной конечной точке. В настоящее время это изменение необходимо выполнить вручную, выполнив следующие действия.

1. Администратор Azure Cosmos DB добавляет или удаляет регионы. Затем Сетевые администраторы получают уведомления об ожидающих изменениях. Частная конечная точка, сопоставленная с учетной записью Azure Cosmos, видит, что ее свойства "Актионсрекуиред" изменились с "нет" на "повторное создание". Затем сетевой администратор обновляет закрытую конечную точку, выдавая запрос на размещение с теми же диспетчер ресурсов полезными данными, которые использовались для его создания.

1. После этой операции необходимо обновить частную службу DNS подсети, чтобы отобразить добавленные или удаленные записи DNS и соответствующие им частные IP-адреса.

Например, при развертывании учетной записи Azure Cosmos в 3 регионах: "Западная часть США", "Центральная часть США" и "Западная Европа". При создании частной конечной точки для вашей учетной записи в подсети зарезервировано 4 частных IP-адресов. По одному для каждого региона, который подсчитывается до 3, а другой — для конечной точки, независимой от глобальной/региона.

Позже, если вы добавите новый регион, например "Восточная часть США", в учетную запись Azure Cosmos. По умолчанию новый регион недоступен из существующей частной конечной точки. Администратор учетной записи Azure Cosmos должен обновить подключение к частной конечной точке, прежде чем обращаться к нему из нового региона.

При выполнении команды ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` выходные данные команды содержат параметр `ActionRequired`, для которого задано повторное создание. Это значение указывает, что необходимо обновить частную конечную точку. Затем администратор учетной записи Azure Cosmos выполняет команду `Set-AzPrivateEndpoint`, чтобы активировать обновление закрытой конечной точки.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Новый частный IP-адрес автоматически резервируется в подсети в этой частной конечной точке, а значение `ActionRequired` становится `None`. Если у вас нет какой-либо частной интеграции с зоной зоне (иными словами, при использовании настраиваемой частной службы DNS), необходимо настроить частный DNS, чтобы добавить новую запись DNS для частного IP-адреса, соответствующего новому региону.

Вы можете использовать те же действия при удалении региона. Частный IP-адрес удаленного региона автоматически освобождается, а флаг `ActionRequired` становится `None`. Если у вас нет частной зоны зоне, необходимо настроить частный DNS, чтобы удалить запись DNS для удаленного региона.

## <a name="current-limitations"></a>Текущие ограничения

При использовании закрытой ссылки с учетной записью Azure Cosmos применяются следующие ограничения.

* При использовании частных ссылок с учетной записью Azure Cosmos в режиме прямого подключения можно использовать только протокол TCP. Протокол HTTP пока не поддерживается

* При использовании API Azure Cosmos DB для учетных записей MongoDB частная конечная точка поддерживается только для учетных записей на сервере версии 3,6 (учетные записи, использующие конечную точку в формате `*.mongo.cosmos.azure.com`). Частная ссылка не поддерживается для учетных записей на сервере версии 3,2 (учетные записи, использующие конечную точку в формате `*.documents.azure.com`). Чтобы использовать частную ссылку, необходимо перенести старые учетные записи в новую версию.

* При использовании API Azure Cosmos DB для учетных записей MongoDB, имеющих закрытую ссылку, нельзя использовать такие средства, как совместная работа 3T, Studio 3T, Mongoose и т. д. Конечная точка может иметь поддержку Private Link, только если указан параметр appName =<account name>. Например: Replica = глобалдб & appName = мидбаккаунтнаме. Так как эти средства не передают имя приложения в строке подключения службе, поэтому нельзя использовать частную ссылку. Однако вы по-прежнему можете получить доступ к этим учетным записям с помощью драйверов SDK с версией 3,6.

* Поддержка закрытых ссылок для учетных записей Azure Cosmos и виртуальных сетей доступна только в конкретных регионах. Список поддерживаемых регионов см. в разделе [Доступные регионы](../private-link/private-link-overview.md#availability) в статье о закрытой ссылке.

* Виртуальную сеть нельзя переместить или удалить, если она содержит закрытую ссылку.

* Учетную запись Cosmos для Azure нельзя удалить, если она подключена к частной конечной точке.

* Невозможно выполнить отработку отказа учетной записи Azure Cosmos в регион, который не сопоставлен со всеми закрытыми конечными точками. Дополнительные сведения см. в разделе Добавление или удаление регионов в предыдущем разделе.

* Администратору сети должно быть предоставлено по крайней мере разрешение "*/Приватиндпоинтконнектионсаппровал" в области действия учетной записи Azure Cosmos с помощью администратора, чтобы создать автоматически утвержденные частные конечные точки.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о других функциях безопасности Azure Cosmos DB см. в следующей статье:

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в разделе [Поддержка брандмауэра](firewall-support.md).

* [Как настроить конечную точку службы виртуальной сети для учетной записи Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Дополнительные сведения о закрытой ссылке см. в документации по [частной ссылке Azure](../private-link/private-link-overview.md) .

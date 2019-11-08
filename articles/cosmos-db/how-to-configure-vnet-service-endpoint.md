---
title: Настройка доступа на основе подсети и виртуальной сети для учетной записи Azure Cosmos DB
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы для виртуальной сети для Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: c399bed803145659bae1863e9e0b919f33254627
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820201"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Настройка доступа из виртуальных сетей (VNet)

Учетные записи Azure Cosmos DB можно настроить для предоставления доступа только из определенной подсети виртуальной сети Azure. Для ограничения доступа к учетной записи Azure Cosmos DB с подключениями из подсети в виртуальной сети выполните следующие шаги:

1. Включите в подсети отправку удостоверения подсети и виртуальной сети в Azure Cosmos DB. Это можно сделать, включив конечную точку службы для Azure Cosmos DB в определенной подсети.

1. Добавьте правило в учетной записи Azure Cosmos DB, определяющее подсеть как источник, из которого можно получить доступ к учетной записи.

> [!NOTE]
> После того как конечная точка службы для учетной записи Azure Cosmos DB будет включена в подсети, источник трафика, подключающийся к Azure Cosmos DB, переключится с общего IP-адреса на виртуальную сеть и подсеть. Переключение трафика применяется к любой учетной записи Azure Cosmos DB, доступ к которой осуществляется из этой подсети. Если в ваших учетных записях Azure Cosmos DB используется брандмауэр на основе IP-адреса для разрешения этой подсети, запросы из подсети с поддержкой службы больше не будут соответствовать правилам брандмауэра для IP-адресов, поэтому будут отклонены.
>
> Дополнительные сведения см. в разделе [Переход от правила брандмауэра для IP-адресов к списку управления доступом виртуальной сети](#migrate-from-firewall-to-vnet) этой статьи.

В следующих разделах описано, как настроить конечную точку службы для виртуальной сети для учетной записи Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Настройка конечной точки службы с помощью портала Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для существующей виртуальной сети Azure и подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.

1. В меню параметров выберите **Брандмауэры и виртуальные сети**, затем в разделе **Выбранные сети** разрешите доступ.

1. Чтобы предоставить доступ к подсети имеющейся виртуальной сети, в разделе **Виртуальные сети** выберите **Add existing Azure virtual network** (Добавление имеющейся виртуальной сети Azure).

1. Выберите **подписку**, из которой требуется добавить виртуальную сеть Azure. Выберите **виртуальную сеть** и **подсети** Azure, которым необходимо предоставить доступ к учетной записи Azure Cosmos DB. Затем выберите **Включить**, чтобы включить выбранные сети с конечными точками службы для Microsoft.AzureCosmosDB. После завершения работы мастера выберите **Добавить**.

   ![Выбор виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. После настройки доступа к учетной записи Azure Cosmos DB из виртуальной сети будет разрешен трафик только из этой выбранной подсети. Добавленная виртуальная сеть и подсеть должны выглядеть так, как показано на следующем снимке экрана:

   ![Успешная настройка виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Чтобы включить конечные точки служб для виртуальной сети, потребуются следующие права доступа к подписке:
>   * подписка с виртуальной сетью — участник сетей;
>   * подписка с учетной записью Azure Cosmos DB — участник учетной записи DocumentDB.
>   * Если ваша виртуальная сеть и учетная запись Azure Cosmos DB находятся в разных подписках, убедитесь, что в подписке с виртуальной сетью также зарегистрирован `Microsoft.DocumentDB` поставщик ресурсов. Сведения о регистрации поставщика ресурсов см. в статье [поставщики и типы ресурсов Azure](../azure-resource-manager/resource-manager-supported-services.md) .

Ниже приведены инструкции по регистрации подписки с помощью поставщика ресурсов.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

1. В меню параметров выберите **Брандмауэры и виртуальные сети Azure**, затем в разделе **Выбранные сети** разрешите доступ.  

1. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе **Виртуальные сети** выберите **Добавить новую виртуальную сеть**.  

1. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите **Создать**. Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   ![Выбор виртуальной сети и подсети для новой виртуальной сети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Если учетная запись Azure Cosmos DB используется другими службами Azure, такими как Azure Когнитивный поиск, или доступ к ней осуществляется из Stream Analytics или Power BI, вы разрешаете доступ, выбрав **принять подключения из в глобальных центрах обработки данных Azure**.

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа с портала Azure**. Дополнительные сведения об этих параметрах см. в статье [Настройка брандмауэра IP-адресов](how-to-configure-firewall.md). После включения доступа выберите **Сохранить** для сохранения настроек.

## <a id="remove-vnet-or-subnet"></a>Удаление виртуальной сети или подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

2. Выберите **Брандмауэры и виртуальные сети** в меню параметров.  

3. Чтобы удалить правило подсети или виртуальной сети, выберите **...** рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   ![Удаление виртуальной сети](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Щелкните **Сохранить**, чтобы применить изменения.

## <a id="configure-using-powershell"></a>Настройка конечной точки службы с помощью Azure PowerShell

> [!NOTE]
> Если вы используете PowerShell или интерфейс командной строки Azure, не забудьте указать полный список фильтров IP-адресов и ACL виртуальной сети в параметрах, а не только те, которые должны быть добавлены.

Для настройки конечной точки службы для учетной записи Azure Cosmos DB с помощью Azure PowerShell выполните следующие действия:  

1. Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Включите конечную точку службы для существующей подсети виртуальной сети.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Получите сведения о виртуальной сети.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Инициализируйте переменные для использования позже. Настройте все переменные из имеющегося определения учетной записи.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Обновите свойства учетной записи Azure Cosmos DB с новой конфигурацией, выполнив следующие командлеты: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Выполните следующую команду, чтобы убедиться, что ваша учетная запись Azure Cosmos DB обновлена с конечной точкой службы для виртуальной сети, настроенной на предыдущем этапе:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Настройка конечной точки службы с помощью интерфейса командной строки Azure

Учетные записи Azure Cosmos можно настроить для конечных точек службы, когда они будут созданы или обновлены позже, если для них уже настроена подсеть. Конечные точки службы также можно включить в учетной записи Cosmos, где для них еще не настроена подсеть, а затем начнет работать, когда подсеть будет настроена позже. Эта гибкость позволяет администраторам, которые не имеют доступа к ресурсам учетной записи Cosmos и виртуальной сети, делать свои конфигурации независимыми друг от друга.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Создайте новую учетную запись Cosmos и подключите ее к внутренней подсети для новой виртуальной сети.

В этом примере виртуальная сеть и подсеть создаются с помощью конечных точек службы, которые будут включены при создании.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Подключение и настройка учетной записи Cosmos для внутренней подсети

В этом примере показано, как подключить учетную запись Azure Cosmos к существующей новой виртуальной сети, в которой подсеть еще не настроена для конечных точек службы. Это делается с помощью параметра `--ignore-missing-vnet-service-endpoint`. Это позволяет завершить настройку учетной записи Cosmos без ошибок до завершения настройки подсети виртуальной сети. После завершения настройки подсети учетная запись Cosmos будет доступна через настроенную подсеть.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>Переход от использования правила брандмауэра для IP-адресов к использованию ACL виртуальной сети

Выполняйте указанные ниже шаги только для учетных записей Azure Cosmos DB с существующими правилами брандмауэра для IP-адресов, разрешающими подсеть, когда нужно использовать виртуальную сеть и ACL на базе подсети вместо правила брандмауэра для IP-адресов.

После включения конечной точки службы для учетной записи Azure Cosmos DB для подсети запросы отправляются в источник, содержащий сведения о виртуальной сети и подсети, а не общедоступный IP-адрес. Эти запросы не соответствуют фильтру IP-адресов. Такое переключение источника происходит для всех учетных записей Azure Cosmos DB, доступ к которым осуществляется из подсети с включенной конечной точкой службы. Во избежание простоя сделайте следующее:

1. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Инициализируйте переменные для использования позже. Настройте все переменные из имеющегося определения учетной записи. Добавьте списки управления доступом виртуальной сети во все учетные записи Azure Cosmos DB, доступ к которым осуществляется из подсети с помощью флага `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Обновите свойства учетной записи Azure Cosmos DB с новой конфигурацией, выполнив следующие командлеты:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Повторите шаги 1–3 для всех учетных записей Azure Cosmos DB, доступ к которым осуществляется из подсети.

1.  Подождите 15 минут, а затем обновите подсеть, чтобы включить конечную точку службы.

1.  Включите конечную точку службы для существующей подсети виртуальной сети.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Удалите правило брандмауэра для IP-адресов для подсети.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в статье [Поддержка брандмауэра](firewall-support.md) для Azure Cosmos DB.

---
title: Настройка доступа на основе виртуальной сети для учетной записи Azure Cosmos
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы для виртуальной сети для Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 995e5a1a87ee332c48641f42c4134e3e58f11cfa
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495426"
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

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Настройка конечной точки службы с помощью портала Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для существующей виртуальной сети Azure и подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.

1. В меню параметров выберите **Брандмауэры и виртуальные сети**, затем в разделе **Выбранные сети** разрешите доступ.

1. Чтобы предоставить доступ к подсети имеющейся виртуальной сети, в разделе **Виртуальные сети** выберите **Add existing Azure virtual network** (Добавление имеющейся виртуальной сети Azure).

1. Выберите **подписку**, из которой требуется добавить виртуальную сеть Azure. Выберите **виртуальную сеть** и **подсети** Azure, которым необходимо предоставить доступ к учетной записи Azure Cosmos DB. Затем выберите **Включить**, чтобы включить выбранные сети с конечными точками службы для Microsoft.AzureCosmosDB. По завершении нажмите кнопку **Добавить**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Выбор виртуальной сети и подсети":::

1. После настройки доступа к учетной записи Azure Cosmos DB из виртуальной сети будет разрешен трафик только из этой выбранной подсети. Добавленная виртуальная сеть и подсеть должны выглядеть так, как показано на следующем снимке экрана:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Успешная настройка виртуальной сети и подсети":::

> [!NOTE]
> Чтобы включить конечные точки служб для виртуальной сети, потребуются следующие права доступа к подписке:
>   * подписка с виртуальной сетью — участник сетей;
>   * подписка с учетной записью Azure Cosmos DB — участник учетной записи DocumentDB.
>   * Если ваша виртуальная сеть и учетная запись Azure Cosmos DB находятся в разных подписках, убедитесь, что в подписке с виртуальной сетью также `Microsoft.DocumentDB` зарегистрирован поставщик ресурсов. Сведения о регистрации поставщика ресурсов см. в статье [поставщики и типы ресурсов Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Ниже приведены инструкции по регистрации подписки с помощью поставщика ресурсов.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

1. В меню параметров выберите **Брандмауэры и виртуальные сети Azure**, затем в разделе **Выбранные сети** разрешите доступ.  

1. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе **Виртуальные сети** выберите **Добавить новую виртуальную сеть**.  

1. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите **Создать**. Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Выбор виртуальной сети и подсети для новой виртуальной сети":::

Если учетная запись Azure Cosmos DB используется другими службами Azure, такими как Azure Когнитивный поиск, или доступ к ней осуществляется из Stream Analytics или Power BI, вы разрешаете доступ, выбрав **принять подключения из в глобальных центрах обработки данных Azure**.

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа с портала Azure**. Дополнительные сведения об этих параметрах см. в статье [Настройка брандмауэра IP-адресов](how-to-configure-firewall.md). После включения доступа выберите **Сохранить** для сохранения настроек.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Удаление виртуальной сети или подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

1. Выберите **Брандмауэры и виртуальные сети** в меню параметров.  

1. Чтобы удалить правило подсети или виртуальной сети, выберите **...** рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Удаление виртуальной сети":::

1. Выберите **Сохранить**, чтобы применить изменения.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Настройка конечной точки службы с помощью Azure PowerShell

> [!NOTE]
> Если вы используете PowerShell или интерфейс командной строки Azure, не забудьте указать полный список фильтров IP-адресов и ACL виртуальной сети в параметрах, а не только те, которые должны быть добавлены.

Для настройки конечной точки службы для учетной записи Azure Cosmos DB с помощью Azure PowerShell выполните следующие действия:  

1. Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Включите конечную точку службы для существующей подсети виртуальной сети.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Получите сведения о виртуальной сети.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Подготовка правила виртуальной сети Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Обновите свойства учетной записи Azure Cosmos DB с помощью новой конфигурации конечной точки виртуальной сети: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Выполните следующую команду, чтобы убедиться, что ваша учетная запись Azure Cosmos DB обновлена с конечной точкой службы для виртуальной сети, настроенной на предыдущем этапе:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Настройка конечной точки службы с помощью интерфейса командной строки Azure

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

В этом примере показано, как подключить учетную запись Azure Cosmos к существующей новой виртуальной сети, в которой подсеть еще не настроена для конечных точек службы. Это делается с помощью `--ignore-missing-vnet-service-endpoint` параметра. Это позволяет завершить настройку учетной записи Cosmos без ошибок до завершения настройки подсети виртуальной сети. После завершения настройки подсети учетная запись Cosmos будет доступна через настроенную подсеть.

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

## <a name="port-range-when-using-direct-mode"></a>Диапазон портов при использовании прямого режима

При использовании конечных точек службы с учетной записью Azure Cosmos через подключение в режиме прямого подключения необходимо убедиться, что в диапазоне TCP-портов от 10000 до 20000 открыт.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Переход от использования правила брандмауэра для IP-адресов к использованию ACL виртуальной сети

Чтобы перенести учетную запись Azure Cosmos DB из использования правил брандмауэра IP-адресов для использования конечных точек службы виртуальной сети, выполните следующие действия.

После того как учетная запись Azure Cosmos DB настроена для конечной точки службы в подсети, запросы из этой подсети отправляются в Azure Cosmos DB с использованием сведений об источнике виртуальной сети и подсети вместо общедоступного IP-адреса источника. Эти запросы больше не будут соответствовать IP-фильтру, настроенному для учетной записи Azure Cosmos DB, поэтому необходимо выполнить следующие действия, чтобы избежать простоев.

Прежде чем продолжать, включите конечную точку службы Azure Cosmos DB в виртуальной сети и подсети, выполнив описанный выше шаг в статье Включение конечной точки службы для существующей подсети виртуальной сети.

1. Получение сведений о виртуальной сети и подсети.

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Подготовка нового объекта правила виртуальной сети для учетной записи Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Обновите учетную запись Azure Cosmos DB, чтобы включить доступ к конечной точке службы из подсети:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Повторите предыдущие шаги для всех Azure Cosmos DB учетных записей, доступных из подсети.

1. Удалите правило брандмауэра IP для подсети из правил брандмауэра учетной записи Azure Cosmos DB.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в статье [Поддержка брандмауэра](firewall-support.md) для Azure Cosmos DB.

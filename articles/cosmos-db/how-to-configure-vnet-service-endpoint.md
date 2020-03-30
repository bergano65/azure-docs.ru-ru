---
title: Настройка виртуального сетевого доступа для учетной записи Azure Cosmos
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы для виртуальной сети для Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366235"
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

1. Выберите **подписку**, из которой требуется добавить виртуальную сеть Azure. Выберите **виртуальную сеть** и **подсети** Azure, которым необходимо предоставить доступ к учетной записи Azure Cosmos DB. Затем выберите **Включить**, чтобы включить выбранные сети с конечными точками службы для Microsoft.AzureCosmosDB. Когда он будет завершен, **выберите Добавить**.

   ![Выбор виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. После настройки доступа к учетной записи Azure Cosmos DB из виртуальной сети будет разрешен трафик только из этой выбранной подсети. Добавленная виртуальная сеть и подсеть должны выглядеть так, как показано на следующем снимке экрана:

   ![Успешная настройка виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Чтобы включить конечные точки служб для виртуальной сети, потребуются следующие права доступа к подписке:
>   * подписка с виртуальной сетью — участник сетей;
>   * подписка с учетной записью Azure Cosmos DB — участник учетной записи DocumentDB.
>   * Если ваша виртуальная сеть и учетная запись Azure Cosmos DB находятся в `Microsoft.DocumentDB` разных подписках, убедитесь, что подписка с виртуальной сетью также зарегистрирована поставщиком ресурсов. Чтобы зарегистрировать поставщика ресурсов, [см.](../azure-resource-manager/management/resource-providers-and-types.md)

Вот направления регистрации подписки у поставщика ресурсов.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

1. В меню параметров выберите **Брандмауэры и виртуальные сети Azure**, затем в разделе **Выбранные сети** разрешите доступ.  

1. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе **Виртуальные сети** выберите **Добавить новую виртуальную сеть**.  

1. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите **Создать**. Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   ![Выбор виртуальной сети и подсети для новой виртуальной сети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Если ваша учетная запись Azure Cosmos DB используется другими службами Azure, такими как Azure Cognitive Search, или доступна из stream Analytics или Power BI, вы разрешаете доступ, выбирая **соединения Accept из глобальных центров обработки данных Azure.**

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа с портала Azure**. Дополнительные сведения об этих параметрах см. в статье [Настройка брандмауэра IP-адресов](how-to-configure-firewall.md). После включения доступа выберите **Сохранить** для сохранения настроек.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Удаление виртуальной сети или подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

1. Выберите **Брандмауэры и виртуальные сети** в меню параметров.  

1. Чтобы удалить правило подсети или виртуальной сети, выберите **...** рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   ![Удаление виртуальной сети](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

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

1. Подготовьте правило виртуальной сети Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Обновление свойств учетной записи Azure Cosmos DB с новой конфигурацией конечных точек Виртуальной сети: 

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

Учетные записи Azure Cosmos могут быть настроены для конечных точек службы, когда они создаются или обновляются позже, если подсеть уже настроена для них. Конечные точки обслуживания также могут быть включены в учетную запись Cosmos, где подсеть еще не настроена для них, а затем начнет работать, когда подсеть будет настроена позже. Такая гибкость позволяет администраторам, не имеющим доступа как к учетной записи Cosmos, так и к виртуальным сетевым ресурсам, делать свои конфигурации независимыми друг от друга.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Создайте новую учетную запись Cosmos и подключите ее к подсети задней части для новой виртуальной сети

В этом примере виртуальная сеть и подсеть создаются с помощью конечных точек обслуживания, включенных для обеих моментов создания.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Подключите и назначаем учетную запись Cosmos в подсеть задней части самостоятельно

Этот пример предназначен для того, чтобы показать, как подключить учетную запись Azure Cosmos к существующей новой виртуальной сети, где подсеть еще не настроена для конечных точек обслуживания. Это делается с `--ignore-missing-vnet-service-endpoint` помощью параметра. Это позволяет завершить конфигурацию учетной записи Cosmos без ошибок до завершения конфигурации в подсети виртуальной сети. После завершения настройки подсети учетная запись Cosmos будет доступна через настроенную подсеть.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Переход от использования правила брандмауэра для IP-адресов к использованию ACL виртуальной сети

Чтобы перенести учетную запись Azure Cosmos DB с использования правил IP-брандмауэра на использование конечных точек виртуального сетевого обслуживания, используйте следующие шаги.

После того, как учетная запись Azure Cosmos DB настроена для конечной точки службы для подсети, запросы из этой подсети отправляются в Azure Cosmos DB с виртуальной информацией об источнике сети и подсети вместо исходного общедоступного IP-адреса. Эти запросы больше не будут соответствовать IP-фильтру, настроенного на учетную запись Azure Cosmos DB, поэтому для предотвращения простоев необходимы следующие действия.

Перед началом работы включите конечную точку службы Azure Cosmos DB в виртуальной сети и подсети, используя указанный выше шаг в "Включить конечную точку службы для существующей подсети виртуальной сети".

1. Получите информацию о виртуальной сети и подсети:

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

1. Подготовьте новый объект правил Виртуальной сети для учетной записи Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Обновление учетной записи Azure Cosmos DB, чтобы обеспечить доступ к конечным точкам службы из подсети:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Повторите предыдущие шаги для всех учетных записей Azure Cosmos DB, доступ к ней из подсети.

1. Удалите правило IP-брандмауэра для подсети из правил Брандмауэра учетной записи Azure Cosmos DB.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в статье [Поддержка брандмауэра](firewall-support.md) для Azure Cosmos DB.

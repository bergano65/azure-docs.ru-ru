---
title: Настройка доступа на основе подсети и виртуальной сети для учетной записи Azure Cosmos DB
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы виртуальной сети для Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 37cc7dcb3157459f6f6387323cc44d6f97954e92
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035820"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Доступ к ресурсам Azure Cosmos DB из виртуальных сетей

Учетные записи Azure Cosmos DB можно настроить для предоставления доступа только из определенной подсети виртуальной сети Azure. Для ограничения доступа к учетной записи Azure Cosmos DB с подключениями из подсети в виртуальной сети выполните следующие шаги:
 
1. Включите в подсети отправку удостоверения подсети и виртуальной сети в Azure Cosmos DB. Это можно сделать, включив конечную точку службы для Azure Cosmos DB в определенной подсети.

1. Добавьте правило в учетной записи Azure Cosmos DB, определяющее подсеть как источник, из которого можно получить доступ к учетной записи.

> [!NOTE]
> После того как конечная точка службы для учетной записи Azure Cosmos DB будет включена в подсети, источник трафика, подключающийся к Azure Cosmos DB, переключится с общего IP-адреса на виртуальную сеть и подсеть. Переключение трафика применяется к любой учетной записи Azure Cosmos DB, доступ к которой осуществляется из этой подсети. Если в ваших учетных записях Azure Cosmos DB используется брандмауэр на основе IP-адреса для разрешения этой подсети, запросы из подсети с поддержкой службы больше не будут соответствовать правилам брандмауэра IP-адресов, поэтому будут отклонены. 
>
> Дополнительные сведения см. в разделе [Переход от правила брандмауэра IP-адресов к списку управления доступом виртуальной сети](#migrate-from-firewall-to-vnet) этой статьи. 

В следующих разделах описано, как настроить конечную точку службы виртуальной сети для учетной записи Azure Cosmos DB.

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
> Чтобы включить конечные точки службы виртуальной сети, потребуются следующие права доступа к подписке:
  * подписка с виртуальной сетью — участник сетей;
  * подписка с учетной записью Azure Cosmos DB — участник учетной записи DocumentDB.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

1. В меню параметров выберите **Брандмауэры и виртуальные сети Azure**, затем в разделе **Выбранные сети** разрешите доступ.  

1. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе **Виртуальные сети** выберите **Добавить новую виртуальную сеть**.  

1. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите **Создать**. Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   ![Выбор виртуальной сети и подсети для новой виртуальной сети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Если ваша учетная запись Azure Cosmos DB используется другими службами Azure, такими как "Поиск Azure", или доступна из Stream Analytics или Power BI, можно разрешить доступ, включив параметр **Принимать подключения из общедоступных центров обработки данных Azure**.

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа с портала Azure**. Дополнительные сведения об этих параметрах см. в статье [Настройка брандмауэра IP-адресов](how-to-configure-firewall.md). После включения доступа выберите **Сохранить** для сохранения настроек.

## <a id="remove-vnet-or-subnet"></a>Удаление виртуальной сети или подсети 

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

2. Выберите **Брандмауэры и виртуальные сети** в меню параметров.  

3. Чтобы удалить правило подсети или виртуальной сети, выберите **...** рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   ![Удаление виртуальной сети](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Щелкните **Сохранить**, чтобы применить изменения.

## <a id="configure-using-powershell"></a>Настройка конечной точки службы с помощью Azure PowerShell

> [!NOTE]
> Если вы используете PowerShell или интерфейс командной строки Azure, не забудьте указать полный список фильтров IP-адресов и ACL виртуальной сети в параметрах, а не только те, которые должны быть добавлены.

Для настройки конечной точки службы для учетной записи Azure Cosmos DB с помощью Azure PowerShell выполните следующие действия:  

1. Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Включите конечную точку службы для существующей подсети виртуальной сети.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Получите сведения о виртуальной сети.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
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

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Выполните следующую команду, чтобы убедиться, что ваша учетная запись Azure Cosmos DB обновлена с конечной точкой службы виртуальной сети, настроенной на предыдущем этапе:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Настройка конечной точки службы с помощью интерфейса командной строки Azure 

1. Включите конечную точку службы для подсети в виртуальной сети.

1. Обновите существующую учетную запись Azure Cosmos DB с помощью списков управления доступом (ACL) подсети.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Создайте учетную запись Azure Cosmos DB с использованием списков управления доступом подсети.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Переход от использования правила брандмауэра IP-адресов к использованию ACL виртуальной сети 

Выполняйте указанные ниже шаги только для учетных записей Azure Cosmos DB с существующими правилами брандмауэра для IP-адресов, разрешающими подсеть, когда нужно использовать виртуальную сеть и ACL на базе подсети вместо правила брандмауэра для IP-адресов.

После включения конечной точки службы для учетной записи Azure Cosmos DB для подсети запросы отправляются в источник, содержащий сведения о виртуальной сети и подсети, а не общедоступный IP-адрес. Эти запросы не соответствуют фильтру IP-адресов. Такое переключение источника происходит для всех учетных записей Azure Cosmos DB, доступ к которым осуществляется из подсети с включенной конечной точкой службы. Во избежание простоя сделайте следующее:

1. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
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

   Set-AzureRmResource `
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

    Get-AzureRmVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
    ```

1. Удалите правило брандмауэра IP-адресов для подсети.

## <a name="next-steps"></a>Дополнительная информация

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в статье [Поддержка брандмауэра](firewall-support.md) для Azure Cosmos DB.

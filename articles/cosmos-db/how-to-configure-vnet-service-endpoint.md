---
title: Настройка виртуальной сети и доступа на основе подсети для вашей учетной записи Azure Cosmos
description: В этом документе описываются шаги, необходимые для настройки конечной точки службы виртуальной сети Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633688"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Получение доступа к ресурсам Azure Cosmos DB из виртуальных сетей

Учетные записи Azure CosmosDB можно настроить для предоставления доступа только из определенной подсети виртуальной сети Azure. Ограничение доступа к учетной записи Azure Cosmos с подключениями из подсети в виртуальной сети выполняется в два шага.
 
1. Включите в подсети отправку удостоверения подсети и виртуальной сети в Azure Cosmos DB. Это можно сделать, включив конечную точку службы для Azure Cosmos DB в определенной подсети.

1. Добавьте правило в учетной записи Azure Cosmos, определяющее подсеть как источник, с которого можно получить доступ к учетной записи.

> [!NOTE]
> После того как конечная точка службы для учетной записи Azure Cosmos будет включена в подсети, источник трафика, подключающийся к Azure Cosmos DB, переключится с общего IP-адреса на виртуальную сеть и подсеть. Переключение трафика применяется к любой учетной записи Azure Cosmos, доступ к которой осуществляется из этой подсети. Если в ваших учетных записях Azure Cosmos используется брандмауэр на основе IP-адреса для разрешения этой подсети, запросы из подсети с поддержкой службы больше не будут соответствовать правилам брандмауэра IP-адресов и, следовательно, будут отклонены. Дополнительные сведения см. в процедуре, описанной в разделе [Переход от правила брандмауэра IP-адресов к списку управления доступом виртуальной сети](#migrate-from-firewall-to-vnet) этой статьи. 

В следующих разделах описано, как настроить конечную точку службы виртуальной сети для учетной записи Azure Cosmos DB.

## <a id="configure-using-portal"></a>Настройка конечной точки службы с помощью портала Azure

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для существующей виртуальной сети Azure и подсети

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos, которую требуется защитить.

1. Из меню параметров выберите **Брандмауэры и виртуальные сети** и затем из раздела "Разрешить доступ" выберите пункт **Выбранные сети**.

1. Чтобы предоставить доступ к подсети имеющейся виртуальной сети, в разделе **Виртуальные сети** выберите **Add existing Azure virtual network** (Добавление имеющейся виртуальной сети Azure).

1. Выберите **Подписка**, из которой требуется добавить виртуальную сеть Azure. Выберите **виртуальную сеть** и **подсети** Azure, которым необходимо предоставить доступ к учетной записи Azure Cosmos. Затем выберите **Включить**, чтобы включить выбранные сети с конечными точками службы для "Microsoft.AzureCosmosDB". После завершения работы мастера выберите **Добавить**. 

   ![Выбор виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. После настройки доступа к учетной записи Azure Cosmos из виртуальной сети будет разрешен трафик только из этой выбранной подсети. Добавленная виртуальная сеть и подсеть должны выглядеть, как показано на следующем снимке экрана:

   ![Успешная настройка виртуальной сети и подсети](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Чтобы включить конечные точки службы виртуальной сети, потребуются следующие права доступа к подписке:
  * подписка с виртуальной сетью — участник сетей;
  * подписка с учетной записью Azure Cosmos — участник учетной записи DocumentDB.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Настройка конечной точки службы для новой виртуальной сети и подсети Azure

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, которую требуется защитить.  

2. Перед включением конечной точки службы виртуальной сети скопируйте информацию брандмауэра IP, связанную с вашей учетной записью Azure Cosmos DB, для использования в будущем. После настройки конечной точки службы можно снова включить брандмауэр IP.  

3. Из меню параметров выберите **Брандмауэры и виртуальные сети** и затем из раздела "Разрешить доступ" выберите пункт **Выбранные сети**.  

4. Чтобы предоставить доступ к новой виртуальной сети Azure, в разделе "Виртуальные сети" выберите **Add new virtual network** (Добавить новую виртуальную сеть).  

5. Укажите сведения, необходимые для создания новой виртуальной сети, а затем выберите "Создать". Подсеть будет создана с конечной точкой службы для включенной "Microsoft.AzureCosmosDB".

   ![Выбор виртуальной сети и подсети для новой виртуальной сети](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Если ваша учетная запись Azure Cosmos используется другими службами Azure, такими как Поиск Azure или доступная из Stream Analytics или Power BI, можно разрешить доступ, установив флажок "Разрешить доступ к службам Azure".

Чтобы иметь доступ к метрикам Azure Cosmos DB с портала, необходимо включить параметр **Разрешение доступа к порталу Azure**. Дополнительные сведения об этих параметрах см. в разделах о запросах с портала Azure и запросах из служб Azure PaaS статьи о настройке [брандмауэра IP-адресов](how-to-configure-firewall.md). После выбора доступа выберите **Сохранить** для сохранения настроек.

## <a id="remove-vnet-or-subnet"></a>Удаление виртуальной сети или подсети 

1. В колонке **Все ресурсы** найдите учетную запись Azure Cosmos DB, для которой назначены конечные точки службы.  

2. Выберите **Брандмауэры и виртуальные сети** из меню параметров.  

3. Чтобы удалить правило подсети или виртуальной сети, выберите "..." рядом с виртуальной сетью или подсетью и нажмите **Удалить**.

   ![Удаление виртуальной сети](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Щелкните **Сохранить**, чтобы применить изменения.

## <a id="configure-using-powershell"></a>Настройка конечной точки службы с помощью Azure PowerShell 

Для настройки конечной точки службы для учетной записи Azure Cosmos DB с помощью Azure PowerShell сделайте следующее:  

1. Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Включите конечную точку службы для существующей подсети виртуальной сети.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Подготовьтесь к операции включения списка управления доступом в учетной записи Azure Cosmos, убедившись в наличии конечной точки службы виртуальной сети и подсети для вашей учетной записи.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Получите свойства учетной записи Azure Cosmos DB, выполнив следующий командлет:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Инициализируйте переменные для использования позже. Настройте все переменные из существующего определения учетной записи. Если у вас есть несколько расположений, необходимо добавить их как часть массива. На этом этапе также настройте конечную точку службы виртуальной сети, установив переменной "accountVNETFilterEnabled" значение "True". Это значение позже будет назначено параметру "isVirtualNetworkFilterEnabled". 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Обновите свойства учетной записи Azure Cosmos DB с новой конфигурацией, выполнив следующие командлеты: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
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

## <a id="configure-using-cli"></a>Настройка конечной точки службы с помощью Azure CLI 

1. Включите конечную точку службы для подсети в виртуальной сети.

1. Обновите имеющуюся учетную запись Azure Cosmos с помощью списков управления доступом подсети.

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Создайте учетную запись Azure Cosmos с помощью списков управления доступом подсети.

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

## <a id="migrate-from-firewall-to-vnet"></a>Переход от правила брандмауэра IP-адресов к списку управления доступом виртуальной сети 

Следующие действия необходимы только для учетных записей Azure Cosmos с имеющимися правилами брандмауэра IP-адресов, разрешающими подсеть, и только в том случае, если вместо правила брандмауэра IP-адресов необходимо использовать виртуальную сеть и списки управления доступом на основе подсети.

После включения конечной точки службы для учетной записи Azure Cosmos для подсети запросы отправляются в источник, содержащий сведения о виртуальной сети и подсети, вместо общедоступного IP-адреса. Поэтому такие запросы не соответствуют IP-фильтру. Такое переключение источника происходит для всех учетных записей Azure Cosmos, доступ к которым осуществляется из подсети с включенной конечной точкой службы. Во избежание простоя сделайте следующее:

1. Получите свойства учетной записи Azure Cosmos, выполнив следующий командлет:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Инициализируйте переменные для использования позже. Настройте все переменные из имеющегося определения учетной записи. Добавьте списки управления доступом виртуальной сети во все учетные записи Cosmos Azure, доступ к которым осуществляется из подсети с помощью флага `ignoreMissingVNetServiceEndpoint`.  

   Если у вас есть несколько расположений, необходимо добавить их как часть массива. На этом этапе также настройте конечную точку службы виртуальной сети, установив переменной "accountVNETFilterEnabled" значение "True". Это значение позже будет назначено параметру "isVirtualNetworkFilterEnabled".

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Измените свойства учетной записи Azure Cosmos с помощью новой конфигурации, выполнив следующие командлеты:

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Повторите шаги 1–3 для всех учетных записей Azure Cosmos, доступ к которым осуществляется из подсети.

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

* Сведения о настройке брандмауэра для Azure Cosmos DB см. в статье [Поддержка брандмауэра для Azure Cosmos DB](firewall-support.md).


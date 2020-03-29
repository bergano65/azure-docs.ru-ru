---
title: Переместите внешний балансер загрузки Azure в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон менеджера ресурсов Azure для перемещения внешнего баланса загрузки Azure из одного региона Azure в другой с помощью Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644823"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Переместите внешний балансер загрузки Azure в другой регион с помощью Azure PowerShell

Существуют различные сценарии, в которых необходимо переместить существующий балансера внешней нагрузки из одного региона в другой. Например, можно создать внешний баланселизатор нагрузки с той же конфигурацией для тестирования. Возможно, также необходимо переместить сальдо внешнего груза в другой регион в рамках планирования аварийного восстановления.

Внешние балансиваторы нагрузки Azure не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон Менеджера ресурсов Azure для экспорта существующей конфигурации и общедоступного IP-адреса внешнего баланса нагрузки.  Затем можно разместить ресурс в другом регионе, экспортируя балансистер нагрузки и публичный IP в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблоны в новом регионе.  Для получения дополнительной информации о [Export resource groups to templates](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) менеджере ресурсов и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что сальдо внешней нагрузки Azure находится в регионе Azure, из которого требуется переместиться.

- Внешние балансиюры нагрузки Azure не могут перемещаться между регионами.  Придется связать новый балансоилизатор нагрузки с ресурсами в целевом регионе.

- Для экспорта конфигурации баланса внешней нагрузки и развертывания шаблона для создания внешнего баланса нагрузки в другом регионе потребуется роль вкладчика сети или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности, публичными ими и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать внешние балансы нагрузки в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления балансеров нагрузки для этого процесса.  Просматривайте [ограничения подписки и обслуживания Azure, квоты и ограничения](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить сальдо внешнего груза для перемещения с помощью шаблона «Менеджер ресурсов» и перенести конфигурацию баланса внешней нагрузки в целевой регион с помощью Azure PowerShell.  В рамках этого процесса, публичная конфигурация IP внешнего баланса нагрузки должны быть включены и должен мне сделать, прежде чем двигаться внешний балансер нагрузки.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Экспорт общедоступного шаблона IP и развертывание с Azure PowerShell

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Получите идентификатор ресурсов общедоступного IP,вы хотите переместиться в целевой регион и поместите его в переменную с помощью [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт исходного общедоступного IP в файл .json в каталог, где вы выполняете команду [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Для отсечения параметра публичного имени IP, измените свойство **defaultValue** имени исходного публичного IP-адреса на имя вашего целевого публичного IP-адреса, убедитесь, что имя находится в кавычках:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Для отображаем целевой области, в которой будет перемещен общественный IP, измените свойство **местоположения** под ресурсами:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Для получения кодов местоположений регионов можно использовать cmdlet Azure PowerShell [Get-AzLocation,](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) запустив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Вы также можете изменить другие параметры в шаблоне, если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Sku** - Вы можете изменить sku общественного IP в конфигурации от стандартного к базовому или базовому к стандарту, изменив свойство **sku** > **имя** в ** \<ресурс-группы имя>.json** файл:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Для получения дополнительной информации о различиях между базовыми и стандартными ip-ips sku [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * **Метод публичного распределения IP** и **тайм-аут простоя** - Вы можете изменить оба этих варианта в шаблоне, изменив свойство **publicIPAllocationMethod** от **Dynamic** к **Static** или Static к **динамическому.** **Dynamic** Время простоя можно изменить, изменив свойство **idleoutInMinutes** на желаемую сумму.  По умолчанию **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Для получения дополнительной информации о методах распределения и значениях простоя тайм-аута [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)


9. Сохранить ** \<ресурс-группу-имя>.json** файл.

10. Создание группы ресурсов в целевом регионе для развертывания целевого публичного IP с помощью [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Развертывание отредактированного ** \<файла группы ресурсов>.json** в группу ресурсов, созданную на предыдущем этапе, с помощью [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Для проверки ресурсов, созданных в целевом регионе, используйте [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Экспорт шаблона баланса внешней нагрузки и развертывание с Azure PowerShell

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурсов сальдо внешней нагрузки, который вы хотите переместить в целевую область, и поместите его в переменную с помощью [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт конфигурации баланса внешней нагрузки в файл .json в каталог, где выполняется команда [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Для отсечения параметра имени сальдо внешнего груза, измените свойство **defaultValue** имени баланса источника внешней нагрузки на имя вашего целевого сальдона внешней нагрузки, убедитесь, что имя находится в кавычках:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Чтобы отсеивать значение целевого публичного IP, которое было перемещено выше, необходимо сначала получить идентификатор ресурса, а затем скопировать и вставить его в файл ** \<>.json.**  Для получения идентификатора используйте [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Введите переменную и нажмите введите для отображения идентификатора ресурса.  Выделите путь id и скопируйте его на буфер обмена:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  В файле ** \<>-группы ресурсов,** вставьте **идентификатор ресурса** из переменной вместо **значения по умолчанию,** во втором параметре для внешнего идентификатора ip-класса, убедитесь, что вы приложить путь в кавычки:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Если вы настроили исходящие правила NAT и исходящих для балансоилира нагрузки, третья запись будет присутствовать в этом файле для внешнего идентификатора для исходящего публичного IP- иС.  Повторите вышеприведенные шаги в **целевом регионе,** чтобы получить идентификатор для исходящего публичного iP и вставьте, что вход в ** \<ресурс-группы имя>.json** файл:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Для отображеный целевой области, в которой будет перемещена конфигурация баланса внешней нагрузки, измените свойство **местоположения** под **ресурсами** в файле ** \<>.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Для получения кодов местоположений регионов можно использовать cmdlet Azure PowerShell [Get-AzLocation,](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) запустив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Вы также можете изменить другие параметры в шаблоне, если вы выберете, и являются необязательными в зависимости от ваших требований:
    
    * **Sku** - Вы можете изменить sku внешнего баланса нагрузки в конфигурации от стандартного к базовому или базовому к стандарту, изменив свойство **sku** > **имя** в ** \<ресурс-группы имя>.json** файл:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Для получения дополнительной информации о различиях между основными [Azure Standard Load Balancer overview](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) и стандартными балансиваторами нагрузки sku, см.

    * **Правила балансировки нагрузки** - Вы можете добавить или удалить правила балансировки нагрузки в конфигурации, добавив или удалив записи в раздел **loadBalancingRules** файла ** \<группы ресурсов>.json:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Для получения дополнительной информации о [What is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) правилах балансировки нагрузки см.

    * **Зонды** - Вы можете добавить или удалить зонд для балансоилира нагрузки в конфигурации, добавив или удалив записи в раздел **зондов** ** \<ресурсной группы>.json** файл:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Для получения дополнительной информации о зондах [Load Balancer health probes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) здоровья Azure Load Balancer см.

    * **Входящие правила NAT** - Вы можете добавить или удалить входящие правила NAT для балансирузана нагрузки, добавив или удалив записи в раздел **inboundNatRules** файла ** \<группы ресурсов>.json:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Для завершения добавления или удаления входящего правила NAT правило должно присутствовать или удаляться в качестве свойства **типа** в конце файла ** \<группы ресурсов>.json:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Для получения дополнительной информации о [What is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) входящих правилах NAT см.

    * **Правила исходящих** - Вы можете добавить или удалить исходящие правила в конфигурации, редактируя свойство **outboundRules** в файле ** \<>.json:**

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Для получения дополнительной информации о правилах исходящего [см.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Сохранить ** \<ресурс-группу-имя>.json** файл.
    
10. Создание или группа ресурсов в целевом регионе для развертывания целевого сальдо внешней нагрузки с помощью [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) Существующая группа ресурсов сверху также может быть повторно использована в рамках этого процесса:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Развертывание отредактированного ** \<файла группы ресурсов>.json** в группу ресурсов, созданную на предыдущем этапе, с помощью [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Для проверки ресурсов, созданных в целевом регионе, используйте [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Игнорировать 

После развертывания, если вы хотите начать более или отказаться от публичного IP и балансиста нагрузки в цели, удалите группу ресурсов, которая была создана в цели, и перемещенный общественный IP и балансомер нагрузки будут удалены.  Чтобы удалить группу ресурсов, используйте [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение NSG, удалите исходную NSG или группу ресурсов, используйте [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) и [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике группа сетевой безопасности Azure перенесла группу сетевой безопасности Azure из одного региона в другой и очистила исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

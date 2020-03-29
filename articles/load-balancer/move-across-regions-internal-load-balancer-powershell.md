---
title: Переместите внутренний балансер загрузки Azure в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон менеджера ресурсов Azure для перемещения баланса внутренней нагрузки Azure из одного региона Azure в другой с помощью Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644279"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Переместите балансировора внутренней нагрузки Azure в другой регион с помощью PowerShell

Существуют различные сценарии, в которых необходимо переместить существующий балансера внутренней нагрузки из одного региона в другой. Например, можно создать внутренний балансиватор нагрузки с той же конфигурацией для тестирования. Возможно, необходимо переместить балансируг внутренней нагрузки в другой регион в рамках планирования аварийного восстановления.

Балансиваторы внутренней нагрузки Azure не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующей конфигурации и виртуальной сети внутреннего балансируза.  Затем можно разместить ресурс в другом регионе, экспортируя балансистер нагрузки и виртуальную сеть в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблоны в новом регионе.  Для получения дополнительной информации о [Export resource groups to templates](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) менеджере ресурсов и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что балансизатор внутренней нагрузки Azure находится в регионе Azure, из которого требуется переместиться.

- Балансиваторы внутренней нагрузки Azure не могут перемещаться между регионами.  Придется связать новый балансоилизатор нагрузки с ресурсами в целевом регионе.

- Для экспорта конфигурации баланса внутренней нагрузки и развертывания шаблона для создания баланса внутренней нагрузки в другом регионе потребуется роль элемента «Сетевой вклад» или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности, виртуальными машинами и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать внутренние балансы нагрузки в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления балансеров нагрузки для этого процесса.  Просматривайте [ограничения подписки и обслуживания Azure, квоты и ограничения](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить баланселизатор внутренней нагрузки к перемещению с помощью шаблона «Менеджер ресурсов» и перенести конфигурацию баланса внутренней нагрузки в целевой регион с помощью Azure PowerShell.  В рамках этого процесса, виртуальная конфигурация сети внутреннего баланса нагрузки должны быть включены и должны быть сделаны в первую очередь перед перемещением внутреннего баланса нагрузки.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Экспорт виртуального шаблона сети и развертывание с Azure PowerShell

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Получите идентификатор ресурсов виртуальной сети, который вы хотите переместить в целевой регион, и поместите его в переменную с помощью [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт фактической сети источника в файл .json в каталог, где вы выполняете команду [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Для отсечения параметра виртуального названия сети, измените свойство **defaultValue** названия исходной виртуальной сети на имя вашей целевой виртуальной сети, убедитесь, что имя находится в кавычках:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Для отображаем область целевого значения, в которой будет перемещен VNET, измените свойство **местоположения** под ресурсами:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. Для получения кодов местоположений регионов можно использовать cmdlet Azure PowerShell [Get-AzLocation,](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) запустив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Вы также можете изменить другие параметры в файле ** \<>группы ресурсов,** если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Адресное пространство** - Адресное пространство VNET может быть изменено до экономии, изменив раздел **addressSpace ресурсов** > **addressSpace** и изменив **свойство адресаPrefixes** в файле ** \<>.json:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnet** - Имя подсети и пространство адреса подсети могут быть изменены или добавлены путем изменения раздела **подсетей** файла ** \<группы ресурсов>.json.** Название подсети может быть изменено путем изменения свойства **имени.** Пространство адреса подсети можно изменить, изменив свойство **адресаPrefix** в файле ** \<>-группы ресурсов:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         В файле ** \<> группы ресурсов,** чтобы изменить префикс адреса, она должна быть отредактирована в двух местах, разделе, указанном выше, и разделе **типа,** указанном ниже.  Измените свойство **адресPrefix,** чтобы соответствовать вышеуказанному:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Сохранить ** \<ресурс-группу-имя>.json** файл.

10. Создание группы ресурсов в целевом регионе для развертывания целевого VNET с помощью [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Развертывание отредактированного ** \<файла группы ресурсов>.json** в группу ресурсов, созданную на предыдущем этапе, с помощью [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Для проверки ресурсов, созданных в целевом регионе, используйте [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Экспорт шаблона внутреннего баланса нагрузки и развертывание с Azure PowerShell

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурсов баланса внутренней нагрузки, который необходимо переместить в целевую область, и поместите его в переменную с помощью [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт ировать конфигурацию баланса внутренней нагрузки источника в файл .json в каталог, где вы выполняете команду [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Для отсечения параметра имени внутреннего балансомливщика нагрузки измените значение свойства **значения** баланса внутренней нагрузки на имя вашего целевого внутреннего балансоилира нагрузки, убедитесь, что имя находится в кавычках:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Чтобы отсеивать значение целевой виртуальной сети, которая была перемещена выше, необходимо сначала получить идентификатор ресурса, а затем скопировать и вставить его в файл ** \<>.json.**  Чтобы получить идентификатор, используйте [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Введите переменную и нажмите введите для отображения идентификатора ресурса.  Выделите путь id и скопируйте его на буфер обмена:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  В файле ** \<>-группы ресурсов** вставьте **идентификатор ресурса** из переменной вместо **значения по умолчанию** во втором параметре для идентификатора целевой виртуальной сети, чтобы обеспечить путь в кавычках:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Для отображеный целевой области, в которой будет перемещена конфигурация баланса внутренней нагрузки, измените свойство **местоположения** под **ресурсами** в файле ** \<>.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
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
    
    * **Sku** - Вы можете изменить sku внутреннего баланса нагрузки в конфигурации от стандартного к базовому или базовому к стандарту, изменив свойство **sku** > **имя** в ** \<ресурс-группы имя>.json** файл:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
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
    
13. Сохранить ** \<ресурс-группу-имя>.json** файл.
    
10. Создание или группа ресурсов в целевом регионе для развертывания целевого баланса внутренней нагрузки с помощью [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) Существующая группа ресурсов сверху также может быть повторно использована в рамках этого процесса:
    
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

После развертывания, если вы хотите начать или отказаться от виртуальной сети и балансиста нагрузки в цель, удалите группу ресурсов, которая была создана в цели, и перемещенная виртуальная сеть и балансер нагрузки будут удалены.  Чтобы удалить группу ресурсов, используйте [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение NSG, удалите исходную НСГ или группу ресурсов, используйте [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) и [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы переместили балансируев внутренней нагрузки Azure из одного региона в другой и очистили исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

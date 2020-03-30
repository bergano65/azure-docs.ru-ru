---
title: Переместите виртуальную сеть Azure в другой регион Azure с помощью Azure PowerShell
description: Переместите виртуальную сеть Azure из одного региона Azure в другой с помощью шаблона «Менеджер ресурсов» и Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646716"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Переместите виртуальную сеть Azure в другой регион с помощью Azure PowerShell

Существуют различные сценарии перемещения существующей виртуальной сети Azure из одного региона в другой. Например, можно создать виртуальную сеть с той же конфигурацией для тестирования и доступности, что и существующая виртуальная сеть. Или вы можете переместить производственную виртуальную сеть в другой регион в рамках планирования аварийного восстановления.

Для завершения перемещения виртуальной сети в другой регион можно использовать шаблон менеджера ресурсов Azure. Вы делаете это, экспортируя виртуальную сеть в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новом регионе. Для получения дополнительной информации о шаблонах «Менеджер ресурсов» [см.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что ваша виртуальная сеть находится в регионе Azure, из которого вы хотите перейти.

- Для экспорта виртуальной сети и развертывания шаблона для создания виртуальной сети в другом регионе необходимо иметь роль вкладчика сети или выше.

- Виртуальные врашители сети не будут воссозданы, и они потерпят неудачу, если они все еще присутствуют в шаблоне. Перед экспортом шаблона необходимо удалить любые виртуальные сетевые узлы. Затем их можно восстановить после перемещения виртуальной сети.
    
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности (NSGs) и публичными ИП.

- Убедитесь, что подписка Azure позволяет создавать виртуальные сети в целевом регионе. Для обеспечения необходимой квоты обратитесь в службу поддержки.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления виртуальных сетей для этого процесса. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Подготовка к переезду
В этом разделе вы готовите виртуальную сеть к перемещению с помощью шаблона Resource Manager. Затем виртуальная сеть перемещается в целевой регион, используя команды Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для экспорта виртуальной сети и развертывания целевой виртуальной сети с помощью PowerShell сделайте следующее:

1. Войдите в подписку Azure с помощью команды [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) а затем следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Получите идентификатор ресурсов виртуальной сети, который вы хотите переместить в целевой регион, а затем поместите его в переменную с помощью [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Экспорт фактической сети источника в файл .json в каталоге, где вы выполняете команду [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Загруженный файл имеет то же имя, что и группа ресурсов, из которого экспортировался ресурс. Найдите файл * \<>группы ресурсов,* который вы экспортируете с помощью команды, а затем откройте его в редакторе:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Чтобы отсеивать параметр названия виртуальной сети, измените свойство **defaultValue** названия исходного виртуального названия сети на имя вашей целевой виртуальной сети. Не забудьте приложить имя в кавычки.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Для отображаем область целевого значения, в которой будет перемещена виртуальная сеть, измените свойство **местоположения** под ресурсами:

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
  
1. Для получения кодов местоположений регионов можно использовать cmdlet Azure PowerShell [Get-AzLocation,](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) запустив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Необязательно) Вы также можете изменить другие параметры в файле * \<>-группы ресурсов,* в зависимости от ваших требований:

    * **Адрес пространства**: Прежде чем сохранить файл, вы можете изменить адресное пространство виртуальной сети, изменив раздел **адрес ресурсовSpace** > **addressSpace** и изменение **адресаPrefixes** свойства:

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

    * **Подсеть**: Вы можете изменить или добавить к имени подсети и расположению адреса подсети, изменив раздел **подсетей** файла. Вы можете изменить название подсети, изменив свойство **имени.** И вы можете изменить пространство адреса подсети, изменив свойство **адресPrefix:**

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

        Чтобы изменить префикс адреса, отспособьте файл в двух местах: в коде в предыдущем разделе и в разделе **типа** следующего кода. Измените свойство **адресаPrefix** в следующем коде, чтобы соответствовать свойству **адресаPrefix** в коде в предыдущем разделе.

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

1. Сохранить * \<ресурс-группу-имя>.json* файл.

1. Создайте группу ресурсов в целевом регионе для развертывания целевой виртуальной сети с помощью [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Развертывание отредактированного * \<файла группы ресурсов>.json* в группу ресурсов, созданную на предыдущем этапе, с помощью [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Чтобы убедиться, что ресурсы были созданы в целевом регионе, используйте [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Удалить виртуальную сеть или группу ресурсов 

После развертывания виртуальной сети, чтобы начать или отказаться от виртуальной сети в целевом регионе, удалите группу ресурсов, созданную в целевом регионе, и перемещенная виртуальная сеть будет удалена. 

Чтобы удалить группу ресурсов, используйте [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Очистка

Чтобы совершить изменения и завершить перемещение виртуальной сети, сделайте любое из следующих:

* Удалите группу ресурсов с помощью [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Удалите исходную виртуальную сеть с помощью [Удалить-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы переместили виртуальную сеть из одного региона в другой с помощью PowerShell, а затем очистили ненужные исходные ресурсы. Подробнее о перемещении ресурсов между регионами и аварийном восстановлении в Azure можно узнать:

- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Переместите виртуальные машины Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

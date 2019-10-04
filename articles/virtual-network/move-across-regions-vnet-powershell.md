---
title: Перемещение виртуальной сети Azure в другой регион Azure с помощью Azure PowerShell
description: Перемещение виртуальной сети Azure из одного региона Azure в другой с помощью шаблона диспетчер ресурсов и Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 753c239f4bf4d6a8f31d4dc5ca771f312cd34578
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828993"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Перемещение виртуальной сети Azure в другой регион с помощью Azure PowerShell

Существует несколько сценариев перемещения существующей виртуальной сети Azure из одного региона в другой. Например, может потребоваться создать виртуальную сеть с такой же конфигурацией для тестирования и доступности, что и у существующей виртуальной сети. Кроме того, вы можете переместить производственную виртуальную сеть в другой регион в рамках планирования аварийного восстановления.

Вы можете использовать шаблон Azure Resource Manager для завершения перемещения виртуальной сети в другой регион. Для этого необходимо экспортировать виртуальную сеть в шаблон, изменить параметры в соответствии с регионом назначения, а затем развернуть шаблон в новом регионе. Дополнительные сведения о шаблонах диспетчер ресурсов см. [в разделе Экспорт групп ресурсов в шаблоны](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что виртуальная сеть находится в регионе Azure, из которого вы хотите переместиться.

- Чтобы экспортировать виртуальную сеть и развернуть шаблон для создания виртуальной сети в другом регионе, необходимо иметь роль участника сети или более высокий.

- Пиринг виртуальных сетей не будет создан повторно, и они завершатся ошибкой, если они все еще находятся в шаблоне. Перед экспортом шаблона необходимо удалить все узлы виртуальных сетей. Затем их можно будет восстановить после перемещения виртуальной сети.
    
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, группами безопасности сети (группы безопасности сети) и общедоступными IP-адресами.

- Убедитесь, что подписка Azure позволяет создавать виртуальные сети в целевом регионе. Чтобы включить требуемую квоту, обратитесь в службу поддержки.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления виртуальных сетей для этого процесса. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Подготовка к перемещению
В этом разделе вы подготовите виртуальную сеть для перемещения с помощью шаблона диспетчер ресурсов. Затем вы перемещаете виртуальную сеть в целевой регион с помощью команд Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы экспортировать виртуальную сеть и развернуть целевую виртуальную сеть с помощью PowerShell, выполните следующие действия.

1. Войдите в подписку Azure с помощью команды [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) , а затем следуйте инструкциям на экране.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Получите идентификатор ресурса виртуальной сети, которую необходимо переместить в целевой регион, а затем поместите ее в переменную с помощью команды [Get-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Экспортируйте исходную виртуальную сеть в файл JSON в каталоге, в котором выполняется команда [Export-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Имя скачанного файла совпадает с именем группы ресурсов, из которой был экспортирован ресурс. Найдите файл *\<resource-Group-name >. JSON* , экспортированный с помощью команды, и откройте его в редакторе:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Чтобы изменить параметр имени виртуальной сети, измените свойство **DefaultValue** исходного имени виртуальной сети на имя целевой виртуальной сети. Не забудьте заключить имя в кавычки.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Чтобы изменить целевой регион, в который будет перемещена виртуальная сеть, измените свойство **Location** в разделе ресурсы:

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
  
1. Чтобы получить коды расположения регионов, можно использовать командлет Azure PowerShell [Get-азлокатион](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) , выполнив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Используемых Можно также изменить другие параметры в файле *\<resource-Group-name >. JSON* в зависимости от требований:

    * **Адресное пространство**. Перед сохранением файла можно изменить адресное пространство виртуальной сети, изменив раздел **resources** > **аддрессспаце** и изменив свойство **аддресспрефиксес** :

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

    * **Подсеть.** Вы можете изменить или добавить имя подсети и адресное пространство подсети, изменив раздел **подсети** файла. Имя подсети можно изменить, изменив свойство **Name** . Адресное пространство подсети можно изменить, изменив свойство **addressPrefix** :

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

        Чтобы изменить префикс адреса, измените файл в двух местах: в коде, приведенном в предыдущем разделе, и в разделе **Type** следующего кода. Измените свойство **addressPrefix** в следующем коде, чтобы оно соответствовало свойству **addressPrefix** в коде в предыдущем разделе.

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

1. Сохраните файл Resource- *Group-name >. JSON. \<*

1. Создайте группу ресурсов в целевом регионе для развертывания целевой виртуальной сети с помощью команды [New-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Разверните измененный файл *\<resource-Group-name >. JSON* в группе ресурсов, созданной на предыдущем шаге, с помощью [New-азресаурцеграупдеплоймент](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Чтобы убедиться, что ресурсы были созданы в целевом регионе, используйте [Get-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Удаление виртуальной сети или группы ресурсов 

После развертывания виртуальной сети для запуска или удаления виртуальной сети в целевом регионе удалите группу ресурсов, созданную в целевом регионе, и перемещенная виртуальная сеть будет удалена. 

Чтобы удалить группу ресурсов, используйте команду [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение виртуальной сети, выполните одно из следующих действий.

* Удалите группу ресурсов с помощью команды [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Удалите исходную виртуальную сеть с помощью [Remove-азвиртуалнетворк](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили виртуальную сеть из одного региона в другой с помощью PowerShell, а затем очистили ненужные исходные ресурсы. Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. в следующих статьях:

- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перемещение виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

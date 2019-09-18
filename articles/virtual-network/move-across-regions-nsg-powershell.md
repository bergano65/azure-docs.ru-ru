---
title: Перемещение группы безопасности сети Azure (NSG) в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон Azure Resource Manager, чтобы переместить группу безопасности сети Azure из одного региона Azure в другой с помощью Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 1be4882af781f884313fbc7b8e2f04f843b60068
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71038957"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Перемещение группы безопасности сети Azure (NSG) в другой регион с помощью Azure PowerShell

Существуют различные сценарии, в которых необходимо переместить существующий группы безопасности сети из одного региона в другой. Например, может потребоваться создать NSG с теми же конфигурацией и правилами безопасности для тестирования. Вы также можете переместить NSG в другой регион в рамках планирования аварийного восстановления.

Группы безопасности Azure нельзя перемещать из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующих правил конфигурации и безопасности NSG.  Затем можно разместить ресурс в другом регионе, экспортировав NSG в шаблон, изменив параметры в соответствии с регионом назначения, а затем развернув шаблон в новом регионе.  Дополнительные сведения о диспетчер ресурсов и шаблонах см. [в разделе Экспорт групп ресурсов в шаблоны](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что группа безопасности сети Azure находится в регионе Azure, из которого вы хотите переместиться.

- Группы безопасности сети Azure нельзя перемещать между регионами.  Необходимо связать новый NSG с ресурсами в целевом регионе.

- Чтобы экспортировать конфигурацию NSG и развернуть шаблон для создания NSG в другом регионе, вам потребуется роль «участник сети» или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, общедоступными IP-адресами и виртуальными сетями.

- Убедитесь, что ваша подписка Azure позволяет создать группы безопасности сети в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления группы безопасности сети для этого процесса.  Ознакомьтесь со статьей [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже показано, как подготовить группу безопасности сети для переноса правила конфигурации и безопасности с помощью шаблона диспетчер ресурсов и переместить конфигурацию NSG и правила безопасности в целевой регион с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Экспорт шаблона и развертывание из скрипта

1. Войдите в подписку Azure с помощью команды [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурса NSG, который необходимо переместить в целевой регион, и поместите его в переменную с помощью [Get-азнетворксекуритиграуп](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспортируйте исходный NSG в JSON-файл в каталог, в котором выполняется команда [Export — азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Скачанный файл будет называться именем группы ресурсов, из которой был экспортирован ресурс.  Выберите файл, который был экспортирован из команды с именем  **\<Resource-Group-name >. JSON** , и откройте ее в любом редакторе:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Чтобы изменить параметр имени NSG, измените значение **DefaultValue** для свойства Source NSG Name на имя целевой NSG, а затем убедитесь, что оно находится в кавычках:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Чтобы изменить целевой регион, в который будут перемещены конфигурация NSG и правила безопасности, измените свойство **Location** в разделе **ресурсы**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Чтобы получить коды расположения регионов, можно использовать командлет Azure PowerShell [Get-азлокатион](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) , выполнив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Кроме того, можно изменить другие параметры в  **\<Resource-Group-name >. JSON** , если вы выбираете и являются необязательными в зависимости от ваших требований:

    * **Правила безопасности** . правила, которые развертываются в целевом NSG, можно изменить, добавив или удалив правила в  **\<разделе securityRules файла resource-Group-name >. JSON** .

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Чтобы завершить добавление или удаление правил в целевом NSG, необходимо также изменить типы настраиваемых правил в конце  **\<файла resource-Group-name >. JSON** в следующем формате:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Сохраните файл Resource- **Group-name >. JSON. \<**

10. Создайте группу ресурсов в целевом регионе для развертывания целевого NSG с помощью [New-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Разверните измененный  **\<файл Resource-Group-name >. JSON** в группе ресурсов, созданной на предыдущем шаге, с помощью команды [New-азресаурцеграупдеплоймент](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0).

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Чтобы проверить, созданы ли ресурсы в целевом регионе, используйте [Get-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-азнетворксекуритиграуп](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Отменить 

Если после развертывания вы хотите начать или отклонить NSG в целевом объекте, удалите группу ресурсов, созданную в целевом объекте, и перемещенный NSG будет удален.  Чтобы удалить группу ресурсов, используйте команду [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы сохранить изменения и завершить перемещение NSG, удалите исходный NSG или группу ресурсов, используйте [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-азнетворксекуритиграуп](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили группу безопасности сети Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

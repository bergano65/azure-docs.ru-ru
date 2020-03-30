---
title: Переместите группу сетевой безопасности Azure (NSG) в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон Azure Resource Manager, чтобы переместить группу сетевой безопасности Azure из одного региона Azure в другой с помощью Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641474"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Переместите группу сетевой безопасности Azure (NSG) в другой регион с помощью Azure PowerShell

Существуют различные сценарии, в которых необходимо переместить существующие НСГ из одного региона в другой. Например, можно создать NSG с той же конфигурацией и правилами безопасности для тестирования. Вы также можете переместить NSG в другой регион в рамках планирования аварийного восстановления.

Группы безопасности Azure не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон менеджера ресурсов Azure для экспорта существующей конфигурации и правил безопасности НСГ.  Затем можно разместить ресурс в другом регионе, экспортируя NSG в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новом регионе.  Для получения дополнительной информации о [Export resource groups to templates](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)ресурсном менеджере и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что группа сетевой безопасности Azure находится в регионе Azure, из которого требуется переместиться.

- Группы безопасности сетей Azure не могут перемещаться между регионами.  Вам придется связать новую NSG с ресурсами в целевом регионе.

- Для экспорта конфигурации NSG и развертывания шаблона для создания NSG в другом регионе вам понадобится роль элемента «Сетевой вклад» или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается балансомназаний нагрузки, публичных I-е и виртуальных сетей.

- Убедитесь, что подписка Azure позволяет создавать НСГ в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления НСУ для этого процесса.  См. дополнительные сведения о [подписке Azure, границах, квотах и ограничениях службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить группу сетевой безопасности к перемещению конфигурации и правил безопасности с помощью шаблона «Менеджер ресурсов» и перенести настройку и правила безопасности NSG в целевой регион с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Экспорт шаблона и развертывание из сценария

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурсов NSG, который вы хотите переместить в целевой регион, и поместите его в переменную с помощью [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт источника NSG в файл .json в каталог, где вы выполняете команду [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Для отсечения параметра имени NSG, измените значение свойства **значения** источника NSG на имя вашей целевой NSG, убедитесь, что имя находится в кавычках:
    
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


6. Для отображаем целевой области, в которой будет перемещена конфигурация NSG и правила безопасности, измените свойство **местоположения** под **ресурсами:**

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
  
7. Для получения кодов местоположений регионов можно использовать cmdlet Azure PowerShell [Get-AzLocation,](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) запустив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Вы также можете изменить другие параметры в ** \<группе ресурсов>.json,** если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Правила безопасности** - Вы можете отсеить, какие правила развернуты в целевой NSG, добавив или удалив правила в раздел **SecurityRules** в файле ** \<> группы ресурсов>.json:**

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

        Для завершения добавления или удаления правил в целевой NSG необходимо также отсеить типы пользовательских правил в конце файла ** \<группы ресурсов>.json** в формате приведенного ниже примера:

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

9. Сохранить ** \<ресурс-группу-имя>.json** файл.

10. Создание группы ресурсов в целевом регионе для развертывания целевой NSG с помощью [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Развертывание отредактированного ** \<файла группы ресурсов>.json** в группу ресурсов, созданную на предыдущем этапе, с помощью [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Для проверки ресурсов, созданных в целевом регионе, используйте [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Игнорировать 

После развертывания, если вы хотите начать более или отказаться от NSG в цели, удалите группу ресурсов, которая была создана в цели, и перемещенная НСГ будет удалена.  Чтобы удалить группу ресурсов, используйте [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение NSG, удалите исходную группу NSG или ресурс, используйте [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике группа сетевой безопасности Azure перенесла группу сетевой безопасности Azure из одного региона в другой и очистила исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

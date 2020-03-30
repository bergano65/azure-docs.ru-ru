---
title: Переместите Ip-адрес Azure Public IP в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон Менеджера ресурсов Azure для перемещения иного иного иного иного иного иного иного иного иного иного иного иного иного иного адреса Azure из одного региона Azure в другой с помощью Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641559"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Переместите Ip-адрес Azure Public IP в другой регион с помощью Azure PowerShell

Существуют различные сценарии, в которых необходимо переместить существующие общедоступные ИП Azure из одного региона в другой. Например, можно создать общедоступный IP с той же конфигурацией и sku для тестирования. Вы также можете переместить общедоступный IP в другой регион в рамках планирования аварийного восстановления.

Публичные испытуемые Azure специфичны для конкретных регионов и не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон Менеджера ресурсов Azure для экспорта существующей конфигурации общедоступного IP-адреса.  Затем можно разместить ресурс в другом регионе, экспортируя общедоступный IP в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новом регионе.  Для получения дополнительной информации о [Export resource groups to templates](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) менеджере ресурсов и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что ИС-адрес Azure Public находится в регионе Azure, из которого требуется переместиться.

- Общедоступные ИС Azure не могут перемещаться между регионами.  Вам придется связать новый общественный ip с ресурсами в целевом регионе.

- Для экспорта общедоступной конфигурации IP и развертывания шаблона для создания общедоступного IP-адреса в другом регионе потребуется роль вкладчика сети или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности (NSGs) и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать общедоступные ИС в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления публичных ИП для этого процесса.  См. дополнительные сведения о [подписке Azure, границах, квотах и ограничениях службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить общедоступный IP для перемещения конфигурации с помощью шаблона resource Manager и переместить конфигурацию IP в целевой регион с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Экспорт шаблона и развертывание из сценария

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурсов общедоступного IP,вы хотите переместиться в целевой регион и поместите его в переменную с помощью [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспорт фактической сети источника в файл .json в каталог, где вы выполняете команду [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Загруженный файл будет назван в честь группы ресурсов, из нее был экспортирован ресурс.  Найдите файл, который был экспортирован из команды под названием ** \<ресурс-группа-имя>.json** и открыть его в редакторе по вашему выбору:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
## <a name="discard"></a>Игнорировать 

После развертывания, если вы хотите начать все сначала или отказаться от публичного IP в целевом, удалите группу ресурсов, которая была создана в целевой цели, и перемещенный публичный IP будет удален.  Чтобы удалить группу ресурсов, используйте [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы совершить изменения и завершить перемещение виртуальной сети, удалите исходную виртуальную сеть или группу ресурсов, используйте [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы перенесли Ip-адрес Azure Public из одного региона в другой и очистили исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

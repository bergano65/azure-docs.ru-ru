---
title: Перемещение общедоступного IP-адреса Azure в другой регион Azure с помощью Azure PowerShell
description: Используйте шаблон Azure Resource Manager, чтобы переместить общедоступный IP-адрес Azure из одного региона Azure в другой с помощью Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: d18dfa7ebed3aefbf6fdb3ffdb6fdd2cf2160cb4
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71038931"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Перемещение общедоступного IP-адреса Azure в другой регион с помощью Azure PowerShell

Существуют различные сценарии, в которых необходимо переместить существующие общедоступные IP-адреса Azure из одного региона в другой. Например, может потребоваться создать общедоступный IP-адрес с такой же конфигурацией и SKU для тестирования. Вы также можете перенести общедоступный IP-адрес в другой регион в рамках планирования аварийного восстановления.

Общедоступные IP-адреса Azure относятся к регионам и не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующей конфигурации общедоступного IP-адреса.  Затем можно разместить ресурс в другом регионе, экспортировав общедоступный IP-адрес в шаблон, изменив параметры в соответствии с регионом назначения, а затем развернув шаблон в новом регионе.  Дополнительные сведения о диспетчер ресурсов и шаблонах см. [в статье экспорт групп ресурсов в шаблоны](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что общедоступный IP-адрес Azure находится в регионе Azure, из которого вы хотите переместиться.

- Общедоступные IP-адреса Azure нельзя перемещать между регионами.  Необходимо связать новый общедоступный IP-адрес с ресурсами в целевом регионе.

- Чтобы экспортировать общедоступную IP-конфигурацию и развернуть шаблон для создания общедоступного IP-адреса в другом регионе, вам потребуется роль "участник сети" или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, группами безопасности сети (группы безопасности сети) и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать общедоступные IP-адреса в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления общедоступных IP-адресов для этого процесса.  Ознакомьтесь со статьей [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже описано, как подготовить общедоступный IP-адрес для перемещения конфигурации с помощью шаблона диспетчер ресурсов и переместить общедоступную IP-конфигурацию в целевой регион с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Экспорт шаблона и развертывание из скрипта

1. Войдите в подписку Azure с помощью команды [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Получите идентификатор ресурса общедоступного IP-адреса, который необходимо переместить в целевой регион, и поместите его в переменную с помощью команды [Get-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Экспортируйте исходную виртуальную сеть в файл JSON в каталог, в котором выполняется команда [Export-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Скачанный файл будет называться именем группы ресурсов, из которой был экспортирован ресурс.  Выберите файл, который был экспортирован из команды с именем  **\<Resource-Group-name >. JSON** , и откройте ее в любом редакторе:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Чтобы изменить параметр имени общедоступного IP-адреса, измените значение свойства **DefaultValue** исходного общедоступного IP-адреса на имя целевого общедоступного IP-адреса, а затем убедитесь, что оно находится в кавычках:
    
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

6. Чтобы изменить целевой регион, в который будет перемещен общедоступный IP-адрес, измените свойство **Location** в разделе ресурсы:

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
  
7. Чтобы получить коды расположения регионов, можно использовать командлет Azure PowerShell [Get-азлокатион](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) , выполнив следующую команду:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Кроме того, можно изменить другие параметры в шаблоне, если вы выбираете и являются необязательными в зависимости от ваших требований:

    * **SKU** . Вы можете изменить номер SKU общедоступного IP-адреса в конфигурации с Standard на Basic или Basic на Standard, изменив свойство**имя** **SKU** > в  **\<файле Resource-Group-name >. JSON** :

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

         Дополнительные сведения о различиях между общедоступными IP-адресами уровня "базовый" и "Стандартный" см. [в статье Создание, изменение или удаление общедоступного адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Метод выделения общедоступного IP-адреса** и **время ожидания простоя** . Вы можете изменить оба этих параметра в шаблоне, изменив свойство **публиЦипаллокатионмесод** с **dynamic** на **static** или **static** на **dynamic.** . Время ожидания простоя можно изменить, изменив свойство **идлетимеаутинминутес** на желаемое значение.  Значение по умолчанию — **4**:

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

        Дополнительные сведения о методах выделения и значениях времени ожидания простоя см. в разделе [Создание, изменение или удаление общедоступного IP-адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Сохраните файл Resource- **Group-name >. JSON. \<**

10. Создайте группу ресурсов в целевом регионе для развертывания целевого общедоступного IP-адреса с помощью [New-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Разверните измененный  **\<файл Resource-Group-name >. JSON** в группе ресурсов, созданной на предыдущем шаге, с помощью команды [New-азресаурцеграупдеплоймент](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0).

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Чтобы проверить, созданы ли ресурсы в целевом регионе, используйте [Get-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) и [Get-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Отменить 

Если после развертывания вы хотите начать или отклонить общедоступный IP-адрес в целевом объекте, удалите группу ресурсов, созданную в целевом объекте, и перемещенный общедоступный IP-адрес будет удален.  Чтобы удалить группу ресурсов, используйте команду [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Очистка

Чтобы сохранить изменения и завершить перемещение виртуальной сети, удалите исходную виртуальную сеть или группу ресурсов, используйте [Remove-азресаурцеграуп](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) или [Remove-азпублиЦипаддресс](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили общедоступный IP-адрес Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

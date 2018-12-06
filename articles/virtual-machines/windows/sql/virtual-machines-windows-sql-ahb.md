---
title: Изменение модели лицензирования для виртуальной машины SQL в Azure | Документация Майкрософт
description: Сведения об изменении модели лицензирования для виртуальной машины SQL в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577063"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>Изменение модели лицензирования для виртуальной машины SQL в Azure
В этой статье описывается процесс, позволяющий изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов SQL **Microsoft.SqlVirtualMachine**. Для виртуальной машины, на которой размещен сервер SQL Server, предлагаются две модели лицензирования — оплата по мере использования или использование собственной лицензии (BYOL). Теперь вы можете изменить выбранную модель лицензирования для виртуальной машины SQL, используя PowerShell или интерфейс командной строки Azure. 

Модель **оплаты по мере использования** означает, что в стоимость секунды выполнения виртуальной машины Azure включается стоимость лицензии SQL Server.

Модель **с использованием собственной лицензии** также называется [Преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Она позволяет использовать собственную лицензию SQL Server для виртуальной машины, на которой работает SQL Server. Дополнительные сведения о ценах см. в [руководстве по ценам на виртуальные машины SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Переключение между двумя моделями лицензирования выполняется **без простоя**, без перезапуска виртуальной машины, **без дополнительных затрат** (более того, включение Преимущества гибридного использования даже снижает затраты) и **вступает в силу немедленно**. 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>Регистрация существующей виртуальной машины SQL в новом поставщике ресурсов
Возможность переключения моделей лицензирования предоставляется только новым поставщиком ресурсов виртуальной машины SQL (Microsoft.SqlVirtualMachine). Сейчас, чтобы переключить модель лицензирования, необходимо предварительно зарегистрировать новый поставщик в подписке и зарегистрировать в этом новом поставщике существующую виртуальную машину SQL. 

  >[!IMPORTANT]
  > В случае удаления ресурса виртуальной машины SQL вы вернетесь к тем настройкам лицензирования, которые запрограммированы в коде образа. 

### <a name="powershell"></a>PowerShell

Следующий фрагмент кода регистрирует новый поставщик ресурсов SQL в используемой подписке, а затем регистрирует существующие виртуальные машины SQL в новом поставщике ресурсов. 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Microsoft Azure
Вы также можете зарегистрировать новый поставщик ресурсов для виртуальной машины SQL с помощью портала. Для этого выполните следующие действия.
1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  

  ![Выберите свою подписку.](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. В колонке **Подписки** перейдите к элементу **Поставщик ресурсов**. 
1. Введите в фильтр значение `sql`, чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие *Зарегистрировать*, *Перерегистрировать* или *Отменить регистрацию* для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

  ![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>С использованием PowerShell 
Вы можете изменить модель лицензирования с помощью PowerShell.  Убедитесь, что виртуальная машина SQL уже зарегистрирована в новом поставщике ресурсов SQL, прежде чем переключать модель лицензирования. 

Следующий фрагмент кода переключает модель оплаты с оплаты по мере использования на использование собственной лицензии (BYOL, или Преимущество гибридного использования Azure): 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Этот фрагмент кода переключает модель лицензирования с BYOL на оплату по мере использования:
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды раньше, чем зарегистрируете виртуальную машину SQL в новом поставщике, может возникнуть такая ошибка: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` If you see this error, please [register your SQL VM with the new resource provider](#register-legacy-SQL-vm-with-new-resource-provider). (Если вы видите эту ошибку, зарегистрируйте виртуальную машину SQL в новом поставщике ресурсов). 
 

## <a name="use-azure-cli"></a>Использование интерфейса командной строки Azure
Вы можете изменить модель лицензирования с помощью Azure CLI.  Убедитесь, что виртуальная машина SQL уже зарегистрирована в новом поставщике ресурсов SQL, прежде чем переключать модель лицензирования. 

Следующий фрагмент кода переключает модель оплаты с оплаты по мере использования на использование собственной лицензии (BYOL, или Преимущество гибридного использования Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Этот фрагмент кода переключает модель лицензирования с BYOL на оплату по мере использования: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды раньше, чем зарегистрируете виртуальную машину SQL в новом поставщике, может возникнуть такая ошибка: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` If you see this error, please [register your SQL VM with the new resource provider](#register-legacy-SQL-vm-with-new-resource-provider). (Если вы видите эту ошибку, зарегистрируйте виртуальную машину SQL в новом поставщике ресурсов). 

## <a name="view-current-licensing"></a>Просмотр текущей модели лицензирования 

Следующий фрагмент кода позволяет просмотреть текущую модель лицензирования для виртуальной машины SQL. 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по ценам SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-pricing-guidance.md).
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).



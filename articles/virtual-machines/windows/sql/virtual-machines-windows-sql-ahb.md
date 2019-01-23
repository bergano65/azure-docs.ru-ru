---
title: Изменение модели лицензирования для виртуальной машины SQL Server в Azure | Документация Майкрософт
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
ms.openlocfilehash: 1b1c7192eb8389d3ad3a1c7c935d9c7e2d8769a9
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359924"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Изменение модели лицензирования для виртуальной машины SQL Server в Azure
В этой статье описывается процесс, позволяющий изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов виртуальной машины SQL **Microsoft.SqlVirtualMachine**. Для виртуальной машины, на которой размещен сервер SQL Server, предлагаются две модели лицензирования — оплата по мере использования или использование собственной лицензии (BYOL). Теперь вы можете изменить выбранную модель лицензирования для виртуальной машины SQL Server, используя PowerShell или интерфейс командной строки Azure. 

Модель **оплаты по мере использования** означает, что в стоимость секунды выполнения виртуальной машины Azure включается стоимость лицензии SQL Server.

Модель **с использованием собственной лицензии** также называется [Преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Она позволяет использовать собственную лицензию SQL Server для виртуальной машины, на которой работает SQL Server. Дополнительные сведения о ценах см. в статье [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Переключение между двумя моделями лицензирования выполняется **без простоя**, без перезапуска виртуальной машины, **без дополнительных затрат** (более того, включение Преимущества гибридного использования Azure даже *снижает* затраты) и **вступает в силу немедленно**. 

## <a name="prerequisites"></a>Предварительные требования
Для использования поставщика ресурсов виртуальной машины SQL требуется расширение SQL IaaS. Таким образом, чтобы продолжить использование поставщика ресурсов виртуальной машины SQL, вам потребуется следующее:
- [Подписка Azure](https://azure.microsoft.com/free/).
- [Виртуальная машина SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) с установленным [расширением SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). 


## <a name="register-existing-sql-server-vm-with-new-resource-provider"></a>Регистрация имеющейся виртуальной машины SQL Server в новом поставщике ресурсов
Возможность переключения моделей лицензирования предоставляется только новым поставщиком ресурсов виртуальной машины SQL (Microsoft.SqlVirtualMachine). Виртуальные машины SQL Server, развернутые после декабря 2018 года, автоматически регистрируются в новом поставщике ресурсов. Тем не менее имеющиеся виртуальные машины, развернутые до этой даты, необходимо вручную зарегистрировать в поставщике ресурсов, прежде чем изменять их модель лицензирования. 




  > В случае удаления ресурса виртуальной машины SQL вы вернетесь к тем настройкам лицензирования, которые запрограммированы в коде образа. 


### <a name="powershell"></a>PowerShell

Следующий фрагмент кода выполняет подключение к Azure и проверяет, какой идентификатор подписки вы используете. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

Следующий фрагмент кода регистрирует новый поставщик ресурсов SQL в используемой подписке, а затем регистрирует имеющуюся виртуальную машину SQL Server в новом поставщике ресурсов. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine


# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Microsoft Azure
Вы также можете зарегистрировать новый поставщик ресурсов для виртуальной машины SQL с помощью портала. Для этого выполните следующие действия.
1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  
1. В колонке **Подписки** перейдите к элементу **Поставщик ресурсов**. 
1. Введите в фильтр значение `sql`, чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие *Зарегистрировать*, *Перерегистрировать* или *Отменить регистрацию* для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

  ![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Использование PowerShell 
Вы можете изменить модель лицензирования с помощью PowerShell.  Убедитесь, что виртуальная машина SQL Server уже зарегистрирована в новом поставщике ресурсов SQL, прежде чем переключать модель лицензирования. 

Следующий фрагмент кода переключает модель оплаты с оплаты по мере использования на использование собственной лицензии (или Преимущество гибридного использования Azure): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

Следующий фрагмент кода переключает модель с использованием собственной лицензии на оплату по мере использования:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды, прежде чем зарегистрируете виртуальную машину SQL Server в новом поставщике, может возникнуть такая ошибка: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Если вы видите эту ошибку, [зарегистрируйте виртуальную машину SQL Server в новом поставщике ресурсов](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Использование интерфейса командной строки Azure
Вы можете изменить модель лицензирования с помощью Azure CLI.  Убедитесь, что виртуальная машина SQL Server уже зарегистрирована в новом поставщике ресурсов SQL, прежде чем переключать модель лицензирования. 

Следующий фрагмент кода переключает модель оплаты с оплаты по мере использования на использование собственной лицензии (или Преимущество гибридного использования Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Следующий фрагмент кода переключает модель с использованием собственной лицензии на оплату по мере использования: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды, прежде чем зарегистрируете виртуальную машину SQL Server в новом поставщике, может возникнуть такая ошибка: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Если вы видите эту ошибку, [зарегистрируйте виртуальную машину SQL Server в новом поставщике ресурсов](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Просмотр текущей модели лицензирования 

Следующий фрагмент кода позволяет просмотреть текущую модель лицензирования для виртуальной машины SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Известные ошибки

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>На виртуальной машине не установлено расширение SQL IaaS
Расширение SQL IaaS является необходимым условием для регистрации виртуальной машины SQL Server в поставщике ресурсов виртуальной машины SQL. Если попытаться зарегистрировать виртуальную машину SQL Server перед установкой расширения SQL IaaS, будет возникать следующая ошибка:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Чтобы устранить эту проблему, установите расширение SQL IaaS, прежде чем предпринимать попытки зарегистрировать виртуальную машину SQL Server. 

  > [!NOTE]
  > Так как установка расширения SQL IaaS приводит к перезапуску службы SQL Server, ее следует выполнять только в период обслуживания. Дополнительные сведения см. в разделе [Установка](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Не удается проверить аргумент в параметре "Sku"
Эта ошибка возникает при попытке изменить модель лицензирования виртуальной машины SQL Server при использовании Azure PowerShell версии 4.0 или более новой:

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Чтобы устранить эту ошибку, раскомментируйте эти строки в приведенном выше фрагменте кода PowerShell при изменении модели лицензирования: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Чтобы проверить версию Azure PowerShell, используйте следующий код:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).



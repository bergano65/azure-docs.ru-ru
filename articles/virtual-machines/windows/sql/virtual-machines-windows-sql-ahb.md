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
ms.openlocfilehash: 8526716b299d26d8d70c9c5e5cdace34e188d019
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111073"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Изменение модели лицензирования для виртуальной машины SQL Server в Azure
В этой статье описывается процесс, позволяющий изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов виртуальной машины SQL **Microsoft.SqlVirtualMachine**. Для виртуальной машины, на которой размещен сервер SQL Server, предлагаются две модели лицензирования — оплата по мере использования или использование собственной лицензии (BYOL). Теперь вы можете изменить выбранную модель лицензирования для виртуальной машины SQL Server, используя PowerShell или интерфейс командной строки Azure. 

Модель **оплаты по мере использования** (PAYG) означает, что в стоимость секунды выполнения виртуальной машины Azure включается стоимость лицензии SQL Server.

Модель **с использованием собственной лицензии** (BYOL) также называется [Преимуществом гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Она позволяет использовать собственную лицензию SQL Server для виртуальной машины, на которой работает SQL Server. Дополнительные сведения о ценах см. в статье [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Переключение между двумя моделями лицензирования выполняется **без простоя**, без перезапуска виртуальной машины, **без дополнительных затрат** (более того, включение Преимущества гибридного использования Azure даже *снижает* затраты) и **вступает в силу немедленно**. 

  >[!NOTE]
  > - Возможность преобразования модели лицензирования в данный момент доступна только для тех клиентов, которые начали работу с образом виртуальной Машины SQL Server с оплатой по мере использования. Если же начать работу с образом с использованием собственной лицензии с портала, вы не сможете преобразовать этот образ для оплаты по мере использования. 
  > - Клиенты CSP могут использовать преимущества AHB, сначала развернув виртуальную машину с оплатой по мере использования, а затем преобразовав ее для использования собственной лицензии. 


## <a name="prerequisites"></a>Предварительные требования
Для использования поставщика ресурсов виртуальной машины SQL требуется расширение SQL IaaS. Таким образом, чтобы продолжить использование поставщика ресурсов виртуальной машины SQL, вам потребуется следующее:
- [Подписка Azure](https://azure.microsoft.com/free/).
- [Виртуальная машина SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) с установленным [расширением SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>Регистрация имеющейся виртуальной машины SQL Server в поставщике ресурсов SQL
Возможность переключения моделей лицензирования предоставляется только новым поставщиком ресурсов виртуальной машины SQL (Microsoft.SqlVirtualMachine). Виртуальные машины SQL Server, развернутые после декабря 2018 года, автоматически регистрируются в новом поставщике ресурсов. Тем не менее имеющиеся виртуальные машины, развернутые до этой даты, необходимо вручную зарегистрировать в поставщике ресурсов, прежде чем изменять их модель лицензирования. 

  > [!NOTE] 
  > В случае удаления ресурса виртуальной машины SQL вы вернетесь к тем настройкам лицензирования, которые запрограммированы в коде образа. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>Регистрация поставщика ресурсов SQL в подписке 

Чтобы зарегистрировать виртуальную машину SQL Server в поставщике ресурсов SQL, необходимо зарегистрировать этот поставщик ресурсов в своей подписке. Для этого можно использовать PowerShell или портал Azure. 

#### <a name="using-powershell"></a>с использованием PowerShell.
Приведенный ниже фрагмент кода зарегистрирует поставщик ресурсов SQL в вашей подпиской Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>Использование портала Azure
Приведенные ниже инструкции позволят зарегистрировать поставщик ресурсов SQL в вашей подпиской Azure. 

1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  
1. В колонке **Подписки** перейдите к элементу **Поставщики ресурсов**. 
1. Введите в фильтр значение `sql`, чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие *Зарегистрировать*, *Перерегистрировать* или *Отменить регистрацию* для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

  ![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Регистрация виртуальной машины SQL Server в поставщике ресурсов SQL
После регистрации поставщика ресурсов SQL в подписке можно использовать PowerShell, чтобы зарегистрировать виртуальную машину SQL Server в этом поставщике ресурсов SQL. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>Использование PowerShell 
Вы можете изменить модель лицензирования с помощью PowerShell.  Убедитесь, что виртуальная машина SQL Server уже зарегистрирована в новом поставщике ресурсов SQL, прежде чем переключать модель лицензирования. 

Следующий фрагмент кода переключает модель оплаты с оплаты по мере использования на использование собственной лицензии (или Преимущество гибридного использования Azure): 
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 

Следующий фрагмент кода переключает модель с использованием собственной лицензии на оплату по мере использования:
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

  >[!NOTE]
  > Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды, прежде чем зарегистрируете виртуальную машину SQL Server в новом поставщике, может возникнуть такая ошибка: `Get-AzResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Если вы видите эту ошибку, зарегистрируйте виртуальную машину SQL Server в новом поставщике ресурсов. 

 

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
  >Чтобы изменить модель лицензирования, необходимо использовать новый поставщик ресурсов виртуальной машины SQL. Если вы попытаетесь выполнить эти команды, прежде чем зарегистрируете виртуальную машину SQL Server в новом поставщике, может возникнуть такая ошибка: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Если вы видите эту ошибку, [зарегистрируйте виртуальную машину SQL Server в новом поставщике ресурсов](#register-existing-sql-server-vm-with-sql-resource-provider). 

## <a name="view-current-licensing"></a>Просмотр текущей модели лицензирования 

Следующий фрагмент кода позволяет просмотреть текущую модель лицензирования для виртуальной машины SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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



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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3f62557d024f56b7014784b6956f15a950f8cca7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926250"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Изменение модели лицензирования для виртуальной машины SQL Server в Azure
В этой статье описывается процесс, позволяющий изменить модель лицензирования для виртуальной машины SQL Server в Azure с помощью нового поставщика ресурсов виртуальной машины SQL **Microsoft.SqlVirtualMachine**. Существуют две модели для виртуальной машины (VM), где размещен SQL Server — по мере использования, лицензирования и использовать собственную лицензию (BYOL). И теперь, с помощью портала Azure, Azure CLI или PowerShell можно изменить какую модель лицензирования использует виртуальную Машину SQL Server. 

**Оплаты по мере использования** модели (PAYG) означает, что стоимость секунды выполнения виртуальной Машины Azure включает в себя стоимость лицензии SQL Server.

**Использовать владельцем лицензии** (BYOL) модели также называется [преимущество (гибридного использования AZURE — Преимущество гибридного на Azure)](https://azure.microsoft.com/pricing/hybrid-benefit/), и он позволяет использовать собственную лицензию SQL Server с помощью виртуальной Машины под управлением SQL Server. Дополнительные сведения о ценах см. в статье [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Переключение между двумя моделями лицензирования выполняется **без простоя**, без перезапуска виртуальной машины, **без дополнительных затрат** (более того, включение Преимущества гибридного использования Azure даже *снижает* затраты) и **вступает в силу немедленно**. 

## <a name="remarks"></a>Примечания


 - Клиенты CSP могут использовать преимущества AHB, сначала развернув виртуальную машину с оплатой по мере использования, а затем преобразовав ее для использования собственной лицензии. 
 - При регистрации пользовательского образа виртуальной Машины SQL Server с поставщиком ресурсов, укажите тип лицензии = «AHUB». При этом лицензии введите как пустой либо указав «PAYG» приведет к регистрации переход на другой. 
 - Если удалить ресурс виртуальной Машины SQL Server, будет выполнен переход обратно параметр жестко лицензии изображения. 
 - Добавление виртуальной Машины SQL Server в группу доступности требует повторного создания ВМ. Как такие, все виртуальные машины, добавление в доступности набора вернусь к типу лицензии с оплатой по мере использования по умолчанию и потребуется снова включить Преимущество гибридного использования AZURE. 
 - Возможность изменить модель лицензирования входит в состав поставщика ресурсов виртуальной Машины SQL. Развертывание образа marketplace на портале Azure автоматически регистрирует виртуальную Машину SQL Server с поставщиком ресурсов. Тем не менее клиенты, которые самостоятельно при установке SQL Server потребуется вручную [зарегистрировать их виртуальной Машине SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Ограничения

 - Возможность преобразования модели лицензирования в данный момент доступна только для тех клиентов, которые начали работу с образом виртуальной Машины SQL Server с оплатой по мере использования. Если же начать работу с образом с использованием собственной лицензии с портала, вы не сможете преобразовать этот образ для оплаты по мере использования.
 - Изменение модели лицензирования поддерживается только для виртуальных машин, развернутых с помощью модели Resource Manager. Виртуальные машины, развернутые с помощью классической модели не поддерживаются. 
 - Изменение модели лицензирования доступна только для установок общедоступного облака.
 - Изменение модели лицензирования, поддерживается только для виртуальных машин с одним сетевым Адаптером (сетевой интерфейс). На виртуальных машинах, имеющих более одной сетевой КАРТЫ, следует сначала удалить один из сетевых адаптеров (с помощью портала Azure) перед выполнением процедуры. В противном случае возникнет ошибка следующего вида: `The virtual machine '\<vmname\>' has more than one NIC associated.` Несмотря на то, что можно добавить сетевой Адаптер на виртуальную машину, после изменения режима лицензирования, операции, выполняемые по колонке конфигурации SQL, таких как автоматическое исправление и резервное копирование, больше не будет считаться поддерживается.

## <a name="prerequisites"></a>Технические условия

Для использования поставщика ресурсов виртуальной машины SQL требуется расширение SQL IaaS. Таким образом, чтобы продолжить использование поставщика ресурсов виртуальной машины SQL, вам потребуется следующее:
- [Подписка Azure](https://azure.microsoft.com/free/).
- [Программы Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Объект *оплаты по мере использования* [виртуальной Машины SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) с [расширения SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) установлен. 

## <a name="with-the-azure-portal"></a>С помощью портала Azure

Вы можете изменить модель лицензирования непосредственно с портала. 

1. Перейдите к виртуальной Машине SQL Server в пределах [портала Azure](https://portal.azure.com). 
1. Выберите **конфигурации SQL Server** в **параметры** области. 
1. Выберите **изменить** в **лицензия SQL Server** панели для изменения лицензии. 

![Преимущество гибридного использования AZURE на портале](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Этот параметр доступен не для образов использовать владельцем лицензии. 

## <a name="with-azure-cli"></a>С помощью Azure CLI

Вы можете изменить модель лицензирования с помощью Azure CLI.  

В следующем фрагменте кода для переключения модели оплаты по мере использования лицензии BYOL (и преимущество гибридного использования Azure):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

В следующем фрагменте кода переключается использовать владельцем лицензии модели оплаты по мере использования: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>С помощью PowerShell
Вы можете изменить модель лицензирования с помощью PowerShell.

В следующем фрагменте кода для переключения модели оплаты по мере использования лицензии BYOL (и преимущество гибридного использования Azure):

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

В следующем фрагменте кода модели BYOL переключается на оплату по мере использования:

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Регистрация виртуальной Машины SQL Server с поставщиком ресурсов виртуальной Машины SQL
В некоторых случаях может потребоваться вручную зарегистрировать виртуальную Машину SQL Server с поставщиком ресурсов виртуальной Машины SQL. Для этого может потребоваться вручную зарегистрировать поставщик ресурсов в подписке. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Зарегистрировать поставщик ресурсов виртуальной Машины SQL в подписке 

Чтобы зарегистрировать виртуальную машину SQL Server в поставщике ресурсов SQL, необходимо зарегистрировать этот поставщик ресурсов в своей подписке. Это можно сделать с помощью портала Azure или Azure CLI. 

#### <a name="with-the-azure-portal"></a>С помощью портала Azure
Следующие действия будут зарегистрировать поставщик ресурсов SQL к своей подписке Azure, с помощью портала Azure. 

1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  
1. В колонке **Подписки** перейдите к элементу **Поставщики ресурсов**. 
1. Введите в фильтр значение `sql`, чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие *Зарегистрировать*, *Перерегистрировать* или *Отменить регистрацию* для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

   ![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>С помощью Azure CLI
В следующем фрагменте кода будет зарегистрировать поставщик ресурсов виртуальной Машины SQL к своей подписке Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>С помощью PowerShell

В следующем фрагменте кода будет зарегистрировать поставщик ресурсов SQL к своей подписке Azure.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Регистрация виртуальной машины SQL Server в поставщике ресурсов SQL
После регистрации поставщика ресурсов виртуальной Машины SQL к своей подписке можно затем зарегистрировать виртуальную Машину SQL Server с поставщиком ресурсов, с помощью Azure CLI. 

#### <a name="with-azure-cli"></a>С помощью Azure CLI
Зарегистрируйте виртуальную Машину SQL Server, с помощью Azure CLI на следующий фрагмент кода: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>С помощью PowerShell
Зарегистрируйте виртуальную Машину SQL Server, с помощью PowerShell следующим фрагментом кода:

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Известные ошибки

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>На виртуальной машине не установлено расширение SQL IaaS
Расширение SQL IaaS является необходимым условием для регистрации виртуальной машины SQL Server в поставщике ресурсов виртуальной машины SQL. Если попытаться зарегистрировать виртуальную машину SQL Server перед установкой расширения SQL IaaS, будет возникать следующая ошибка:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Чтобы устранить эту проблему, установите расширение SQL IaaS, прежде чем предпринимать попытки зарегистрировать виртуальную машину SQL Server. 

  > [!NOTE]
  > Так как установка расширения SQL IaaS приводит к перезапуску службы SQL Server, ее следует выполнять только в период обслуживания. Дополнительные сведения см. в разделе [Установка](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Ресурс "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<группы ресурсов >" в группе ресурсов "\<группы ресурсов >" не найден. Не удалось найти свойство «sqlServerLicenseType» для этого объекта. Убедитесь, что свойство существует и можно задать.
Эта ошибка возникает при попытке изменить модель лицензирования на виртуальную Машину SQL Server, который не был зарегистрирован с помощью поставщика ресурсов SQL. Вам потребуется зарегистрировать поставщик ресурсов должен вашей [подписки](#register-sql-vm-resource-provider-with-subscription), а затем зарегистрировать виртуальную Машину SQL Server в SQL [поставщика ресурсов](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Не удается проверить аргумент в параметре "Sku"
Эта ошибка возникает при попытке изменить модель лицензирования виртуальной машины SQL Server при использовании Azure PowerShell версии 4.0 или более новой: SET-AzResource: Не удается проверить аргумент в параметре «Sku». Аргумент имеет значение null или пустым. Укажите аргумент, который не является пустым или равным NULL и затем попробуйте еще раз.
Чтобы устранить эту ошибку, раскомментируйте эти строки в приведенном выше фрагменте кода PowerShell при изменении модели лицензирования:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Чтобы проверить версию Azure PowerShell, используйте следующий код:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).



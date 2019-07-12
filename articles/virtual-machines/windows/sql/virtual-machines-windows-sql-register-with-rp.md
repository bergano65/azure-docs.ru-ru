---
title: Регистрация поставщика ресурсов виртуальной Машины SQL виртуальной машины SQL Server в Azure | Документация Майкрософт
description: Зарегистрируйте виртуальную Машину SQL Server с поставщиком ресурсов виртуальной Машины SQL для повышения управляемости.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786751"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Регистрация виртуальной машины SQL Server в Azure с поставщиком ресурсов виртуальной Машины SQL

В этой статье описывается, как зарегистрировать Azure SQL Server виртуальной машины (VM) с поставщиком ресурсов виртуальной Машины SQL. 

Развертывание образа виртуальной Машины SQL Server marketplace на портале Azure автоматически регистрирует виртуальную Машину SQL Server с поставщиком ресурсов. Тем не менее если вы решили самостоятельно установить SQL Server на виртуальных машинах Azure вместо выбора образа из Azure Marketplace, необходимо зарегистрировать виртуальную Машину SQL Server с поставщиком ресурсов для:

-  **Соответствие** — в условиях для продуктов Microsoft, клиенты, использующие [Преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) необходимо указать в корпорацию Майкрософт при использовании гибридного использования Azure — и сделать это, их необходимо зарегистрировать поставщика ресурсов виртуальной Машины SQL. 

-  **Функция преимущества** -регистрация виртуальной Машины SQL Server с поставщиком ресурсов разблокирует [автоматическая установка исправлений](virtual-machines-windows-sql-automated-patching.md), [Автоматическая архивация](virtual-machines-windows-sql-automated-backup-v2.md), возможности мониторинга и управляемость, а также [лицензирования](virtual-machines-windows-sql-ahb.md) и [edition](virtual-machines-windows-sql-change-edition.md) гибкость. Ранее они были доступны только для образов виртуальных Машин SQL Server из Azure Marketplace.

Самостоятельной установки SQL Server на виртуальной Машине Azure или подготовки виртуальной Машины Azure из пользовательского VHD-файла с помощью SQL Server требованиям с помощью Преимущество гибридного использования Azure для SQL Server с условием, что клиенты указывают, использующие в корпорацию Майкрософт, зарегистрировав ресурс виртуальной Машины SQL Поставщик. 

Чтобы использовать поставщик ресурсов виртуальной Машины SQL, необходимо также зарегистрировать поставщик ресурсов виртуальной Машины SQL с вашей подпиской. Это можно сделать с помощью портала Azure, Azure CLI и PowerShell. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы зарегистрировать поставщика ресурсов виртуальной Машины SQL Server, необходимо следующее: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- Объект [виртуальной Машины SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) и [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Зарегистрировать поставщик ресурсов виртуальной Машины SQL
Если [расширения SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) уже установлен на виртуальной Машине, а затем Регистрация поставщика ресурсов виртуальной Машины SQL просто создать ресурс метаданных типа Microsoft.SqlVirtualMachine/SqlVirtualMachines. Ниже приведен фрагмент кода для регистрации с поставщиком ресурсов виртуальной Машины SQL, если расширение IaaS для SQL уже установлен на виртуальной Машине. Необходимо указать тип лицензии SQL Server, требуемого при регистрации поставщика ресурсов виртуальной Машины SQL либо как "(PAYG)" или «AHUB». 

Зарегистрируйте виртуальную Машину SQL Server, с помощью PowerShell следующим фрагментом кода:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Если расширение IaaS для SQL не установлен на виртуальной Машине, затем можно зарегистрировать с поставщиком ресурсов виртуальной Машины SQL, указав упрощенный режим управления SQL. В упрощенном режиме управления SQL, виртуальной Машины SQL, поставщик ресурсов будет автоматически установить расширение IaaS для SQL в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) и проверка SQL Server метаданные экземпляра; значение может не перезапустите службу SQL Server. Необходимо указать тип лицензии SQL Server, требуемого при регистрации поставщика ресурсов виртуальной Машины SQL либо как "(PAYG)" или «AHUB». 

Зарегистрируйте виртуальную Машину SQL Server в упрощенном режиме управления SQL, с помощью PowerShell следующим фрагментом кода:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
Регистрация поставщика ресурсов виртуальной Машины SQL в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) будет гарантировать соответствие и включить гибкие лицензирования а также обновление выпуска SQL Server на месте. Экземпляры отказоустойчивого кластера и развертывании на нескольких экземплярах могут быть зарегистрированы с поставщиком ресурсов виртуальной Машины SQL только в упрощенном режиме. Необходимо выполнить инструкции на портале Azure, чтобы обновить до [полный режим](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) и включить набор функций полную управляемость при перезапуске SQL Server в любое время. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Регистрация SQL Server 2008/R2 на виртуальных машинах Windows Server 2008

SQL Server 2008 и 2008 на Windows Server 2008 R2 могут быть зарегистрированы с виртуальной Машиной SQL, для предоставления ресурсов в [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) режим. Этот параметр гарантирует соответствие требованиям и позволяет виртуальной Машине SQL Server для мониторинга на портале Azure с ограниченной функциональностью.

В приведенной ниже таблице представлены допустимые значения для параметров, предоставленного во время регистрации:

| Параметр | Допустимые значения                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, или `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` или `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Чтобы зарегистрировать в SQL Server 2008 или 2008 R2 в экземпляре Windows Server 2008, используйте следующий фрагмент кода Powershell:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Проверьте состояние регистрации
Вы можете проверить, если SQL Server уже был зарегистрирован с поставщиком ресурсов виртуальной Машины SQL с помощью портала Azure, Azure CLI или PowerShell. 

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)
Чтобы проверить состояние регистрации, с помощью портала Azure, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). 
1. Перейдите к вашей [виртуальные машины SQL](virtual-machines-windows-sql-manage-portal.md).
1. Выберите виртуальную Машину SQL Server из списка. Если виртуальной Машине SQL Server нет в списке, весьма вероятно, виртуальной Машине SQL Server не был зарегистрирован с поставщиком ресурсов виртуальной Машины SQL. 
1. Просмотрите значение в разделе `Status`. Если `Status = Succeeded`, а затем виртуальную Машину SQL Server с поставщиком ресурсов виртуальной Машины SQL успешно зарегистрирован. 

    ![Проверить состояние регистрации поставщика Ресурсов SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ ИНТЕРФЕЙСА КОМАНДНОЙ СТРОКИ](#tab/bash)

Проверьте текущее состояние регистрации виртуальной Машины SQL Server следующей командой AZ интерфейса командной строки. `ProvisioningState` будет показано `Succeeded` Если регистрация прошла успешно. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Проверьте текущее состояние регистрации виртуальной Машины SQL Server с помощью следующего командлета PowerShell. `ProvisioningState` будет показано `Succeeded` Если регистрация прошла успешно. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Ошибка указывает, что виртуальная машина SQL Server не был зарегистрирован с поставщиком ресурсов. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Зарегистрировать поставщик ресурсов виртуальной Машины SQL в подписке 

Чтобы зарегистрировать виртуальную Машину SQL Server с поставщиком ресурсов виртуальной Машины SQL, необходимо зарегистрировать поставщика ресурсов для вашей подписки. Это можно сделать с помощью портала Azure или Azure CLI.

# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)


Следующие действия будут зарегистрировать поставщик ресурсов виртуальной Машины SQL к своей подписке Azure, с помощью портала Azure. 

1. Войдите на портал Azure и откройте раздел **Все службы**. 
1. Перейдите к разделу **Подписки** и выберите нужную подписку.  
1. На **подписок** страницы, перейдите к **поставщиков ресурсов**. 
1. Введите в фильтр значение `sql`, чтобы отсортировать поставщиков ресурсов, имеющих отношение к SQL. 
1. Выберите действие *Зарегистрировать*, *Перерегистрировать* или *Отменить регистрацию* для поставщика **Microsoft.SqlVirtualMachine** в зависимости от ваших намерений. 

   ![Изменение поставщика](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ ИНТЕРФЕЙСА КОМАНДНОЙ СТРОКИ](#tab/bash)

В следующем фрагменте кода будет зарегистрировать поставщик ресурсов виртуальной Машины SQL к своей подписке Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

В следующем фрагменте кода PowerShell будет зарегистрировать поставщик ресурсов виртуальной Машины SQL к своей подписке Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Примечания

 - Поставщик ресурсов виртуальной Машины SQL поддерживает только виртуальные машины SQL Server, развернутых с помощью Resource Manager. Виртуальные машины SQL Server развернут с помощью классической модели не поддерживаются. 
 - Поставщик ресурсов виртуальной Машины SQL поддерживает только виртуальные машины SQL Server, развернутых в общедоступное облако. Развертывания частного или облако для государственных организаций, не поддерживаются. 

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).



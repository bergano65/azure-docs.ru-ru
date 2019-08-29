---
title: Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server IaaS | Документация Майкрософт
description: В этой статье описывается, как управлять расширением агента IaaS SQL Server, которое автоматизирует определенные задачи администрирования SQL Server. К ним относятся автоматическая архивация, автоматическая установка исправлений и интеграция Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100478"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server IaaS
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье представлен обзор служб, поддерживаемых расширением. В этой статье также приводятся инструкции по установке, состоянию и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Чтобы просмотреть классическую версию этой статьи, см. раздел [SQL Server расширение агента IaaS для SQL Server виртуальных машин (классическая модель)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Существует три режима управления для расширения SQL Server IaaS: 

- **Полный** режим обеспечивает все функциональные возможности, но требует перезапуска разрешений SQL Server и системного администратора. Этот вариант установлен по умолчанию. Используйте его для управления виртуальной машиной SQL Server с одним экземпляром. 

- Для облегчения не требуется перезапуск SQL Server, но он поддерживает только изменение типа лицензии и выпуска SQL Server. Используйте этот параметр для SQL Server виртуальных машин с несколькими экземплярами или для участия в экземпляре отказоустойчивого кластера (FCI). 

- Для SQL Server 2008 и SQL Server 2008 R2, установленных на Windows Server 2008, используется специальный **Агент** . Сведения об использовании этого режима для образа Windows Server 2008 см. в разделе [регистрация Windows server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматическая архивация SQL Server** |Автоматизирует планирование резервного копирования для всех баз данных либо для экземпляра по умолчанию, либо [правильно установленного](virtual-machines-windows-sql-server-iaas-faq.md#administration) именованного экземпляра SQL Server на виртуальной машине. Дополнительные сведения см. [в статье Автоматическое резервное копирование для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server автоматизированной установки исправлений** |Настраивает период обслуживания, во время которого можно установить на виртуальную машину важные обновления ОС Windows. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. [в статье автоматическая установка исправлений для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-sql-automated-patching.md). |
| **Интеграция Azure Key Vault** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. [в статье Настройка интеграции Azure Key Vault для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

После установки и запуска расширения агента SQL Server IaaS становятся доступны функции администрирования:

* На панели SQL Server виртуальной машины в портал Azure и Azure PowerShell для образов SQL Server в Azure Marketplace.
* С помощью Azure PowerShell для установки расширения вручную. 

## <a name="prerequisites"></a>Предварительные требования
Ниже приведены требования к использованию расширения агента SQL Server IaaS на виртуальной машине.

**Операционная система**.

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Версия SQL Server**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**

* [Скачайте и настройте последние команды Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Режимы управления изменениями

Текущий режим агента SQL Server IaaS можно просмотреть с помощью PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server виртуальные машины, на которых установлено облегченное расширение IaaS, могут обновить режим до _полного_ с помощью портал Azure. SQL Server виртуальные машины в режиме _без агента_ можно обновить до _полной_ версии после обновления операционной системы до Windows 2008 R2 и более поздних версий. Переход на использование более ранней версии невозможен. необходимо полностью удалить расширение SQL IaaS и снова установить его. 

Чтобы обновить режим агента до уровня Full, выполните следующие действия. 


# <a name="azure-portaltabazure-portal"></a>[портал Azure](#tab/azure-portal)

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к ресурсу [виртуальных машин SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Выберите SQL Server виртуальную машину и щелкните **Обзор**. 
1. Для SQL Server виртуальных машин с режимом "без агента" или "упрощенный IaaS" выберите **только тип лицензии и обновления выпуска, доступные в сообщении расширения SQL IaaS** .

   ![Варианты изменения режима на портале](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Установите флажок **я принимаю, чтобы перезапустить службу SQL Server на виртуальной машине** , а затем нажмите кнопку **подтвердить** , чтобы обновить режим IaaS до полного. 

    ![Флажок для согласия на перезапуск службы SQL Server на виртуальной машине](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[Azure CLI](#tab/bash)

Выполните следующий фрагмент кода AZ CLI:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Выполните следующий фрагмент кода PowerShell:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Установка
Расширение SQL Server IaaS устанавливается при регистрации SQL Server виртуальной машины с помощью [поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). При необходимости агент SQL Server IaaS можно установить вручную с помощью полного или упрощенного режима. 

Расширение агента SQL Server IaaS в полноэкранном режиме автоматически устанавливается при подготовке одной из SQL Server виртуальных машин Azure Marketplace с помощью портал Azure. 

### <a name="install-in-full-mode"></a>Установка в полном режиме
Полный режим для расширения IaaS SQL Server обеспечивает полную управляемость для одного экземпляра на виртуальной машине SQL Server. Если экземпляр по умолчанию существует, расширение будет работать с экземпляром по умолчанию и не будет поддерживать управление другими экземплярами. Если экземпляр по умолчанию отсутствует, но только один именованный экземпляр, он будет управлять именованным экземпляром. Если экземпляр по умолчанию отсутствует и существует несколько именованных экземпляров, установить расширение не удастся. 

Установите SQL Server агент IaaS с полным режимом с помощью PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Параметр | Допустимые значения                        |
| :------------------| :-------------------------------|
| **склсерверлиценсетипе** | `AHUB` или `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Если расширение еще не установлено, установка полного расширения перезапускает службу SQL Server. Чтобы не перезапускать службу SQL Server, установите упрощенный режим с ограниченной управляемостью.
> 
> Обновление расширения SQL Server IaaS не приводит к перезапуску службы SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Установка на виртуальной машине с одним именованным экземпляром SQL Server
Расширение SQL Server IaaS будет работать с именованным экземпляром на SQL Server, если экземпляр по умолчанию удален, а расширение IaaS будет переустановлено.

Чтобы использовать именованный экземпляр SQL Server, выполните следующие действия.
   1. Развертывание SQL Server виртуальной машины из Azure Marketplace. 
   1. Удалите расширение IaaS из [портал Azure](https://portal.azure.com).
   1. Полностью удалите SQL Server в SQL Server виртуальной машине.
   1. Установите SQL Server с именованным экземпляром в виртуальной машине SQL Server. 
   1. Установите расширение IaaS из портал Azure.  


### <a name="install-in-lightweight-mode"></a>Установка в упрощенном режиме
Упрощенный режим не приведет к перезапуску службы SQL Server, но предлагает ограниченную функциональность. 

Установите агент IaaS SQL Server в упрощенном режиме с помощью PowerShell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Параметр | Допустимые значения                        |
| :------------------| :-------------------------------|
| **склсерверлиценсетипе** | `AHUB` или `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Получение состояния расширения IaaS SQL Server
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. Выберите **все параметры** в окне виртуальной машины, а затем щелкните **расширения**. В списке будет указано расширение **SqlIaasExtension**.

![Состояние расширения агента IaaS SQL Server в портал Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Можно также использовать командлет **Get-азвмсклсерверекстенсион** Azure PowerShell.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Предыдущая команда подтверждает, что агент установлен и предоставляет общие сведения о состоянии. Вы можете получить конкретные сведения о состоянии автоматизированного резервного копирования и исправлений с помощью следующих команд:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Удаление
В портал Azure можно удалить расширение, нажав кнопку с многоточием в окне **расширения** свойств виртуальной машины. Теперь щелкните **Удалить**.

![Удаление расширения агента IaaS SQL Server в портал Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-азвмсклсерверекстенсион** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Следующие шаги
Начните использовать одну из служб, которую поддерживает расширение. Дополнительные сведения см. в статьях, указанных в разделе " [Поддерживаемые службы](#supported-services) " этой статьи.

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в разделе [что такое SQL Server на виртуальных машинах Azure?](virtual-machines-windows-sql-server-iaas-overview.md).


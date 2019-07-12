---
title: Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента IaaS для SQL Server | Документация Майкрософт
description: В этой статье описывается управление расширением агента SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798056"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента IaaS для SQL Server
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье представлен обзор и служб, поддерживаемых расширением, а также инструкции по установке, проверке состояния и удаления.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. здесь: [Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Существует три режима управляемости SQL для расширения SQL IaaS: **Полный**, **упрощенного**, и **NoAgent**. 

- **Полный** режим предоставляет все функциональные возможности, но требует перезапуска разрешения на SQL Server и SA. Это параметр, который устанавливается по умолчанию и должен использоваться для управления виртуальной Машины SQL Server с одним экземпляром. 

- **Упрощенный** требует перезапуска SQL Server, но поддерживает только изменить тип лицензии и выпуск SQL Server. Этот параметр должен иметь используется для виртуальных машин SQL Server с несколькими экземплярами или участвующие в экземпляре отказоустойчивого кластера (FCI). 

- **NoAgent** выделяется для SQL Server 2008 и SQL Server 2008 R2 на Windows Server 2008. Сведения об использовании `NoAgent` режим для образа Windows Server 2008, см. в разделе [регистрации Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматическая архивация SQL** |Автоматизирует планирование архивации всех баз данных для любого экземпляра по умолчанию или [правильно установленному](virtual-machines-windows-sql-server-iaas-faq.md#administration) именованный экземпляр SQL Server на виртуальной Машине. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Автоматическая установка исправлений SQL** |Настраивает период обслуживания, во время которого можно установить на виртуальную машину важные обновления ОС Windows. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Интеграция с хранилищем ключей Azure** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

После установки и запуска расширение агента IaaS SQL Server делает доступными эти функции администрирования на панели SQL Server виртуальной машины на портале Azure через оболочку Azure PowerShell для образов Marketplace SQL Server, а также через оболочку Azure PowerShell для ручной установки расширения. 

## <a name="prerequisites"></a>Предварительные требования
Требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

**Операционная система**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Версии SQL Server**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**

* [Скачайте и настройте последние команды Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Изменение режимов управления

Текущий режим агента SQL IaaS можно просмотреть с помощью PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Для виртуальных машин SQL Server, имеют *NoAgent* или *упрощенных* после установки расширения IaaS вы можете обновить режим, в котором *полный* с помощью портала Azure. Это не позволяет понизить — чтобы сделать это, необходимо полностью удалить расширение SQL IaaS и установите его повторно. 

Режим обновления агента для *полный*, выполните следующие действия: 

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите к вашей [виртуальные машины SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) ресурсов. 
1. Выберите виртуальную машину SQL Server и выберите **Обзор**. 
1. Для виртуальных машин SQL с помощью *NoAgent* или *упрощенных* режимы IaaS, выберите сообщение для **только с помощью расширения SQL IaaS доступны лицензии типа и выпуск обновления**.

    ![Запустить изменение режима с портала](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Принять **перезапустите службу SQL Server** , установив флажок, а затем выберите **Подтверждение** для обновления вашей режима с IaaS, значение «full». 

    ![Включить полное управление для расширения IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Установка
Установлено расширение SQL IaaS, при регистрации виртуальной Машины SQL Server с [поставщика ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Тем не менее, при необходимости, агента SQL IaaS также можно установить вручную с помощью *полный* или *упрощенных* режим установки. 

*Полный* расширение агента IaaS SQL Server устанавливается автоматически при подготовке одного из образов коллекции виртуальных машин SQL Server, с помощью портала Azure. 

### <a name="full-mode-installation"></a>Полный режим установки
*Полный* расширения SQL IaaS предлагает полный управляемости для одного экземпляра на виртуальной Машине SQL Server. Если экземпляр по умолчанию, затем расширение будет работать с экземпляром по умолчанию, и она не будет поддерживать управление другими экземплярами. Если экземпляр не по умолчанию, но только один именованный экземпляр, он будет управлять именованного экземпляра. Если нет экземпляра по умолчанию и несколько именованных экземпляров, то расширение завершится ошибкой для установки. 

Установка *полный* режим SQL IaaS перезапускает службу SQL Server. Чтобы избежать перезапуска службы SQL Server, установите *упрощенных* режиме с ограниченную управляемости, вместо этого. 

Установка агента SQL IaaS с *полный* режиме с помощью PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Параметр | Допустимые значения                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, или `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Если модуль не установлен, установка **полный** расширение перезапускает службу SQL Server. Используйте **упрощенных** режиме, чтобы избежать перезапуска службы SQL Server. 
> - Обновление расширения SQL IaaS не перезапускает службу SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Установка на виртуальную Машину с одного именованного экземпляра SQL Server
Расширение SQL IaaS будет работать с именованным экземпляром, на SQL Server, если экземпляр по умолчанию удаляется и переустановить расширение IaaS.

Чтобы использовать именованный экземпляр SQL Server, сделайте следующее:
   1. Разверните виртуальную Машину SQL Server из marketplace. 
   1. Удаление расширения IaaS изнутри [портала Azure](https://portal.azure.com).
   1. Удаление SQL Server в виртуальной Машине SQL Server.
   1. Установка SQL Server с именованным экземпляром, в виртуальной Машине SQL Server. 
   1. Установите расширение IaaS из на портале Azure.  


### <a name="install-in-lightweight-mode"></a>Установить в упрощенном режиме
Упрощенный режим не будет перезапустить службу SQL Server, но он обеспечивает ограниченные функциональные возможности. 

Установка агента SQL IaaS с *упрощенных* режиме с помощью PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Параметр | Допустимые значения                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, или `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Получить состояние расширения SQL IaaS
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В окне виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SqlIaasExtension**.

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Вы также можете использовать командлет Azure PowerShell **Get-AzVMSqlServerExtension**.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Предыдущая команда подтверждает, что агент установлен, и предоставляет общие сведения о состоянии. С помощью следующих команд можно получить определенные сведения о состоянии автоматической архивации и установки исправлений.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Удаление
На портале Azure, расширение можно удалить, щелкнув кнопку с многоточием **расширения** окно свойств виртуальной машины. Затем щелкните **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-AzVMSqlServerExtension**.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Следующие шаги
Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в статьях, на которые ссылается [поддерживаемые службы](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).


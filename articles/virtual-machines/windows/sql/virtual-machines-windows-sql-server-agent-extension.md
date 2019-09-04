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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3240bb689447c16de8c62e9e8118b0b0df2b1ea3
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259427"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server IaaS
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье представлен обзор служб, поддерживаемых расширением. В этой статье также приводятся инструкции по установке, состоянию и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Чтобы просмотреть классическую версию этой статьи, см. раздел [SQL Server расширение агента IaaS для SQL Server виртуальных машин (классическая модель)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


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


##  <a name="installation"></a>Установка
Расширение SQL Server IaaS устанавливается при регистрации SQL Server виртуальной машины с помощью [поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). При необходимости можно установить агент SQL Server IaaS вручную с помощью следующей команды PowerShell: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> При установке расширения перезапускается служба SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Установка на виртуальной машине с одним именованным экземпляром SQL Server
Расширение SQL Server IaaS будет работать с именованным экземпляром на SQL Server, если экземпляр по умолчанию удален, а расширение IaaS будет переустановлено.

Чтобы использовать именованный экземпляр SQL Server, выполните следующие действия.
   1. Развертывание SQL Server виртуальной машины из Azure Marketplace. 
   1. Удалите расширение IaaS из [портал Azure](https://portal.azure.com).
   1. Полностью удалите SQL Server в SQL Server виртуальной машине.
   1. Установите SQL Server с именованным экземпляром в виртуальной машине SQL Server. 
   1. Установите расширение IaaS из портал Azure.  


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

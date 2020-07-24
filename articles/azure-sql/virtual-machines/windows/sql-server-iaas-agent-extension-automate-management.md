---
title: Автоматизация задач управления с помощью расширения агента IaaS
description: В этой статье описывается управление расширением агента IaaS для SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 638a7fa96f4112f9805fd46618dc0acd1c2f382f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063235"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента IaaS для SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. в статье о [расширении агента IaaS для SQL Server виртуальных машин SQL Server (классическая)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматическое резервное копирование SQL Server** |Автоматизирует планирование резервного копирования всех баз данных для экземпляра по умолчанию или [правильно установленного](frequently-asked-questions-faq.md#administration) именованного экземпляра SQL Server на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Автоматическое резервное копирование для виртуальных машин SQL Server (Resource Manager)](automated-backup-sql-2014.md). |
| **Автоматическая установка исправлений SQL Server** |Настраивает период обслуживания, во время которого можно установить на виртуальную машину важные обновления ОС Windows. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](automated-patching.md). |
| **Интеграция с Azure Key Vault** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции Azure Key Vault для SQL Server на виртуальных машинах Azure (Resource Manager)](azure-key-vault-integration-configure.md). |

После установки и запуска расширения агента IaaS для SQL Server функции администрирования становятся доступными:

* на панели SQL Server виртуальной машины на портале Azure через оболочку Azure PowerShell для образов SQL Server в Azure Marketplace;
* через оболочку Azure PowerShell для ручной установки расширения. 

## <a name="prerequisites"></a>Предварительные требования
Ниже приведены требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

**Операционная система**.

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Версия SQL Server**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**

* [Скачайте и настройте последние команды Azure PowerShell](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Установка
Расширение SQL Server IaaS устанавливается при регистрации SQL Server виртуальной машины с помощью [поставщика ресурсов SQL Server виртуальной машины](sql-vm-resource-provider-register.md). При необходимости можно установить агент IaaS для SQL Server вручную, выполнив следующую команду PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> При установке расширения перезапускается служба SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Установка на виртуальной машине с одним именованным экземпляром SQL Server
Расширение IaaS для SQL Server будет работать с именованным экземпляром на SQL Server, если экземпляр по умолчанию удален, а расширение IaaS установлено повторно.

Чтобы использовать именованный экземпляр SQL Server, выполните следующие действия:
   1. Разверните виртуальную машину SQL Server из Azure Marketplace. 
   1. Удалите расширение IaaS на [портале Azure](https://portal.azure.com).
   1. Полностью удалите SQL Server в виртуальной машине SQL Server.
   1. Установите SQL Server с именованным экземпляром в виртуальной машине SQL Server. 
   1. Удалите расширение IaaS на портала Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Получение сведений о состоянии расширения IaaS для SQL Server
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В окне виртуальной машины выберите **Все параметры**, а затем — **Расширения**. В списке будет указано расширение **SqlIaasExtension**.

![Состояние расширения IaaS для SQL Server на портале Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

Вы также можете использовать командлет Azure PowerShell **Get-AzVMSqlServerExtension**:

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
Чтобы удалить расширение на портале Azure, в колонке **Расширения** в свойствах виртуальной машины щелкните значок с многоточием. Теперь щелкните **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-AzVMSqlServerExtension**:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Дальнейшие действия
Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в статье [Что собой представляет SQL Server на виртуальных машинах Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

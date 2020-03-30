---
title: Автоматизация задач управления с расширением агента IaaS
description: В этой статье описывается, как управлять расширением агента S'L Server IaaS, которое автоматизирует выполнение конкретных задач администрирования сервера S'L Server. К ним относятся автоматическое резервное копирование, автоматическое исправление и интеграция Azure Key Vault.
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030784"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента S'L Server IaaS
> [!div class="op_single_selector"]
> * [Менеджер ресурсов](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье содержится обзор служб, поддерживающих расширение. В этой статье также содержатся инструкции по установке, статусу и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Для просмотра классической версии этой статьи, [см.](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)


## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматизированное резервное копирование сервера S'L** |Автоматизирует планирование резервных ups для всех баз данных для экземпляра по умолчанию или [правильно установленного](virtual-machines-windows-sql-server-iaas-faq.md#administration) названного экземпляра сервера S'L на VM. Для получения дополнительной информации [см. Автоматизированное резервное копирование для сервера S'L в виртуальных машинах Azure (менеджер ресурсов).](virtual-machines-windows-sql-automated-backup.md) |
| **Автоматизированное исправление сервера S'L** |Настраивает период обслуживания, во время которого можно установить на виртуальную машину важные обновления ОС Windows. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Для получения дополнительной информации [см. Автоматизированное исправление сервера S'L в виртуальных машинах Azure (менеджер ресурсов).](virtual-machines-windows-sql-automated-patching.md) |
| **Интеграция Azure Key Vault** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Для получения дополнительной информации см. [Нанастройку интеграции Azure Key Vault для сервера S'L на виртуальных машинах Azure (менеджер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

После установки и запуска агента сервера Iaas, это делает функции администрирования доступными:

* На панели сервера s'L виртуальной машины на портале Azure и через Azure PowerShell для изображений сервера S'L на Azure Marketplace.
* Через Azure PowerShell для ручных установок расширения. 

## <a name="prerequisites"></a>Предварительные требования
Вот требования к использованию расширения агента Сервера IaaS на вашем VM:

**Операционная система**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Версия сервера S'L**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Скачать и настроить последние команды Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Установка
Расширение S'L Server IaaS устанавливается при регистрации ВМ сервера S'L с [поставщиком ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md) При необходимости можно установить агент S'L Server IaaS вручную, используя нижеприведенную команду PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Установка расширения перезапускает службу сервера S'L. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Установка на VM с одним экземпляром под названием S'L Server
Расширение S'L Server IaaS будет работать с указанным экземпляром на сервере S'L, если экземпляр по умолчанию не установлен и расширение IaaS будет переустановлено.

Чтобы использовать именованный экземпляр сервера S'L, выполните следующие действия:
   1. Развертывание VM сервера S'L от Azure Marketplace. 
   1. Удалите расширение IaaS с [портала Azure](https://portal.azure.com).
   1. Полностью удалите сервер S'L в рамках VM сервера S'L.
   1. Установка сервера S'L с именованным экземпляром в VM сервера S'L. 
   1. Установите расширение IaaS с портала Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Получите статус расширения сервера IaaS
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. Выберите **все настройки** в окне виртуальной машины, а затем выберите **расширения.** В списке будет указано расширение **SqlIaasExtension**.

![Статус расширения агента сервера IaaS на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Вы также можете использовать **Get-AzVMSqlServerExtension** Azure PowerShell cmdlet:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Предыдущая команда подтверждает, что агент установлен, и предоставляет общую информацию о состоянии. Вы можете получить конкретную информацию о состоянии автоматического резервного копирования и исправления с помощью следующих команд:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Удаление
На портале Azure можно удалить расширение, выбрав эллипсис в окне **расширения** свойств виртуальной машины. Затем выберите **Удалить**.

![Установка расширения агента сервера IaaS на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать **Remove-AzVMSqlServerExtension** PowerShell cmdlet:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Дальнейшие действия
Начните использовать одну из служб, которую поддерживает расширение. Для получения дополнительной информации смотрите статьи, упомянутые в разделе [Поддерживаемые службы](#supported-services) этой статьи.

Для получения более подробной информации о запуске [What is SQL Server on Azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md)сервера S'L на виртуальных машинах Azure см.

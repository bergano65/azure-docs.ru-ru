---
title: Автоматизация задач управления на виртуальных машинах SQL (модель с использованием Resource Manager) | Документы Майкрософт
description: В этой статье описывается управление расширением агента SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d3e9b3b2db4a4adc01ad3b1f348b66496658f0f5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190934"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server (модель с использованием Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SqlIaasExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. здесь: [Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматическая архивация SQL** |Автоматизирует планирование архивации всех баз данных для любого экземпляра по умолчанию или [правильно установленному](virtual-machines-windows-sql-server-iaas-faq.md#administration) именованный экземпляр SQL Server на виртуальной Машине. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Автоматическая установка исправлений SQL** |Настраивает период обслуживания, во время которого можно установить на виртуальную машину важные обновления ОС Windows. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Интеграция с хранилищем ключей Azure** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

После установки и запуска расширение агента IaaS SQL Server делает доступными эти функции администрирования на панели SQL Server виртуальной машины на портале Azure через оболочку Azure PowerShell для образов Marketplace SQL Server, а также через оболочку Azure PowerShell для ручной установки расширения. 

## <a name="prerequisites"></a>Технические условия
Требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

**Операционная система**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
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

> [!IMPORTANT]
> В настоящее время [расширение агента IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) не поддерживается для экземпляра отказоустойчивого кластера SQL Server в Azure. Мы рекомендуем удалить расширение с виртуальных машин, которые участвуют в экземпляре отказоустойчивого кластера. Возможности, поддерживаемые расширением, недоступны для виртуальных машин SQL после удаления агента.

## <a name="installation"></a>Установка
Расширение агента IaaS для SQL Server автоматически устанавливается при подготовке одного из образов коллекции виртуальных машин SQL Server. Расширение SQL IaaS предлагает управляемости для одного экземпляра на виртуальной Машине SQL Server. Если экземпляр по умолчанию, затем расширение будет работать с экземпляром по умолчанию, и она не будет поддерживать управление другими экземплярами. Если экземпляр не по умолчанию, но только один именованный экземпляр, он будет управлять именованного экземпляра. Если нет экземпляра по умолчанию и несколько именованных экземпляров, то расширение завершится ошибкой для установки. 



Если необходимо переустановить расширение вручную на одной из этих виртуальных машин SQL Server, используйте следующую команду PowerShell:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Если расширение не установлено, при его установке перезапускается служба SQL Server. При обновлении расширения SQL IaaS служба SQL Server не перезапускается. 

> [!NOTE]
> Хотя можно установить расширение агента IaaS SQL Server для пользовательских образов SQL Server, функциональные возможности в настоящее время ограничены [изменение типа лицензии](virtual-machines-windows-sql-ahb.md). Другие возможности, предоставляемые с помощью расширения SQL IaaS будут работать только на [образов из коллекции виртуальной Машины SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (Оплата по мере использования или перевести your собственной лицензии).

### <a name="use-a-single-named-instance"></a>Использовать один именованный экземпляр
Расширение SQL IaaS будут работать с именованным экземпляром, в образ SQL Server, экземпляр по умолчанию в случае удаления правильно, и в случае переустановки расширения IaaS.

Чтобы использовать именованный экземпляр SQL Server, сделайте следующее:
   1. Разверните виртуальную Машину SQL Server из marketplace. 
   1. Удаление расширения IaaS изнутри [портала Azure](https://portal.azure.com).
   1. Удаление SQL Server в виртуальной Машине SQL Server.
   1. Установка SQL Server с именованным экземпляром, в виртуальной Машине SQL Server. 
   1. Установите расширение IaaS из на портале Azure.  

## <a name="status"></a>Status
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В окне виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SqlIaasExtension**.

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Вы также можете использовать командлет Azure PowerShell **Get-AzVMSqlServerExtension**.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Предыдущая команда подтверждает, что агент установлен, и предоставляет общие сведения о состоянии. С помощью следующих команд можно получить определенные сведения о состоянии автоматической архивации и установки исправлений.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Удаление
На портале Azure, расширение можно удалить, щелкнув кнопку с многоточием **расширения** окно свойств виртуальной машины. Затем щелкните **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-AzVMSqlServerExtension**.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Дальнейшие действия
Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).


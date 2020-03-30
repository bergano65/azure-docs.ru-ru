---
title: Изменения документации для сервера S'L на виртуальных машинах Azure Документы Майкрософт
description: Узнайте о новых функциях и усовершенствованиях сервера S'L на Azure VM
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201651"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Изменения документации для сервера S'L на виртуальных машинах Azure

Azure позволяет развертывать виртуальную машину (VM) со встроенным изображением сервера S'L. В этой статье кратко излагаются изменения документации, связанные с новыми функциями и улучшениями в последних выпусках [сервера S'L на виртуальных машинах Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/) 


## <a name="january-2020"></a>Январь 2020 г.

| Изменения | Сведения |
| --- | --- |
| **Поддержка правительства Azure** | Теперь можно зарегистрировать виртуальные машины S'L Server с помощью поставщика ресурсов S'L VM для виртуальных машин, размещенных в облаке [правительства Azure.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Изменения | Сведения |
 --- | --- |
| **Бесплатная реплика DR в Azure** | Вы можете разместить [бесплатный пассивный экземпляр](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) для аварийного восстановления в Azure для вашего внутреннего экземпляра S-L Server, если у вас есть [Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3) | 
| **Регистрация поставщика массовых ресурсов** | Теперь вы можете [регистрировать](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) виртуальные машины s'L с поставщиком ресурсов. | 
|**Оптимизированная конфигурация хранения данных** | Теперь вы можете [полностью настроить конфигурацию хранилища](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) при создании нового VM сервера S'L. |
|**Премиум-доля файлов для FCI** | Теперь можно создать экземпляр кластера failover, используя [Premium File Share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) вместо исходного метода Пространства для хранения [direct.](virtual-machines-windows-portal-sql-create-failover-cluster.md) 
| **Специальный хост Azure** | Вы можете запустить свой VM сервера S'L на [специальном хосте Azure.](virtual-machines-windows-sql-dedicated-host.md) | 
| **Переместите ВМ СЗЛ в другой регион** | Используйте восстановление сайта Azure для [переноса ВМ сервера S'L из одного региона в другой.](virtual-machines-windows-sql-move-different-region.md) |
|  **Новые режимы установки S'L IaaS** | Теперь можно установить расширение S'L Server IaaS в [легком режиме,](virtual-machines-windows-sql-server-agent-extension.md) чтобы избежать перезапуска службы S'L Server.  |
| **Модификация издании сервера S'L** | Теперь вы можете изменить [свойство издания](virtual-machines-windows-sql-change-edition.md) для вашего S'L Server VM. |
| **Изменения в поставщике ресурсов ВМ** | Вы можете [зарегистрировать свой S'L Server VM с помощью поставщика ресурсов S'L VM,](virtual-machines-windows-sql-register-with-resource-provider.md) используя новые режимы S'L IaaS. Эта возможность включает изображения [Windows Server 2008.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Принесите изображения с собственной лицензией с помощью Azure Hybrid Benefit** | Принесите изображения, развернутые с Azure Marketplace, которые теперь могут переключаться на [тип лицензии на оплату по мере вашего узла.](virtual-machines-windows-sql-ahb.md#remarks)| 
| **Новое управление VM сервера S'L на портале Azure** | Теперь на портале Azure есть способ управления VM сервером S'L. Для получения дополнительной информации на [портале Azure можно ознакомиться на портале «Управляйте vMs-серверами S'L Server».](virtual-machines-windows-sql-manage-portal.md)  | 
| **Расширенная поддержка сервера S'L 2008/2008 R2** | [Расширьте поддержку](virtual-machines-windows-sql-server-2008-eos-extend-support.md) сервера 2008 и Сервера 2008 R2, перемив, *как* и в Azure VM. | 
| **Пользовательская поддержка изображения** | Теперь вы можете установить [расширение S'L Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) на пользовательские изображения ОС и СЗЛ, которое предлагает ограниченную функциональность [гибкого лицензирования.](virtual-machines-windows-sql-ahb.md) При регистрации пользовательского изображения в поставщике ресурсов S'L укажите тип лицензии как "AHUB". В противном случае регистрация не будет. | 
| **Именованная поддержка экземпляра** | Теперь можно использовать [расширение S'L Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) с указанным экземпляром, если экземпляр по умолчанию был неправильно установлен. | 
| **Улучшение портала** | Для улучшения удобства использования был обновлен портал Azure для развертывания VM сервера S'L Server. Для получения дополнительной информации ознакомьтесь с кратким [быстрым запуском](quickstart-sql-vm-create-portal.md) и более тщательным [руководством](virtual-machines-windows-portal-sql-server-provision.md) по развертыванию VM сервера S'L.|
| **Улучшение портала** | Теперь можно изменить модель лицензирования для VM сервера S'L с оплаты по мере использования, чтобы принести свою собственную лицензию с помощью [портала Azure.](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)|
| **Упрощение развертывания группы доступности с помощью Azure S'L Server VM CLI** | Теперь развертывать группу доступности в VM сервера S'L в Azure стало проще, чем когда-либо. Вы можете использовать [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) для создания кластера сбоя Windows, внутреннего баланса нагрузки и слушателей группы доступности из командной строки. Для получения дополнительной информации [см.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Изменения | Сведения |
| --- | --- |
|  **Новый поставщик ресурсов для кластера серверов S'L** | Новый поставщик ресурсов (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) определяет метаданные кластера Windows failover. Присоединение к S'L Server VM к *slVirtualMachineGroups* загружает службу Windows Server Failover Cluster (WSFC) и присоединяется к VM в кластере.  |
| **Автоматизированная настройка развертывания группы доступности с шаблонами быстрого запуска Azure** |Теперь можно создать кластер совок Windows, присоединиться к виможно свистому серверу S'L Server, создать слушателя и настроить баланселист внутренней нагрузки с двумя шаблонами быстрого запуска Azure. Для получения дополнительной информации [см. Используйте шаблоны быстрого запуска Azure, чтобы настроить группу всегда на доступность для сервера S'L на Azure VM.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **Автоматическая регистрация поставщику ресурсов ВМ** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. ВМ сервера S'L, развернутые до этого месяца, все еще должны быть зарегистрированы вручную. Для получения более подробной информации посетите [регистрацию виртуальной машины сервера S'L в Azure с поставщиком ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md)|
|**Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов (Microsoft.SqlVirtualMachine) обеспечивает более эффективное управление вашими VMs-серверами. Для получения более подробной информации [Register a SQL Server virtual machine in Azure with the SQL VM resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)о регистрации виртуальных виртуальных технологий, см. |
|**Переключение модели лицензирования** | Теперь вы можете переключаться между моделями с оплатой за использование и привозить собственные лицензии для вашего S'L Server VM с помощью Azure CLI или PowerShell. Для получения дополнительной информации [см. Как изменить модель лицензирования для виртуальной машины сервера S'L в Azure.](virtual-machines-windows-sql-ahb.md) | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Дополнительные ресурсы

**Windows VMs**:

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Подготовка виртуальной машины SQL Server на базе Windows](virtual-machines-windows-portal-sql-server-provision.md).
* [Миграция базы данных на сервер S'L на Azure VM](virtual-machines-windows-migrate-sql.md)
* [Высокая доступность и аварийное восстановление сервера S'L на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Рекомендации по производительности для сервера S'L на виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
* [Шаблоны приложений и стратегии разработки сервера S'L на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VMs**:

* [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
* [Обеспечение виртуальной машины S'L Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../../linux/sql/sql-server-linux-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Сервер S'L на Linux документации](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

---
title: Изменения в документации по SQL Server на виртуальных машинах Azure | Документация Майкрософт
description: Узнайте о новых возможностях и улучшениях для SQL Server на виртуальных машинах Azure.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 43b2a02acc04a5c81b9e7bfbe3bfb1551b48ed52
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964623"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Изменения в документации по SQL Server на виртуальных машинах Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure позволяет развертывать виртуальные машины со встроенным образом SQL Server. В этой статье перечислены изменения в документации, связанные с новыми функциями и улучшениями в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="june-2020"></a>июнь 2020 г.


| Изменения | Сведения |
| --- | --- |
| **Имя распределенной сети (DNN)** | SQL Server 2019 в Windows Server 2016 + теперь является предварительным просмотром поддержки маршрутизации трафика к экземпляру отказоустойчивого кластера (FCI) с использованием [имени распределенной сети](hadr-distributed-network-name-dnn-configure.md) , а не с помощью Azure Load Balancer. Эта поддержка упрощает и ускоряет подключение к вашему решению высокого уровня доступности в Azure. | 
| **FCI с общими дисками Azure** | Теперь можно развернуть [экземпляр отказоустойчивого кластера (FCI)](failover-cluster-instance-overview.md) с помощью [общих дисков Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) с SQL Server 2019 на виртуальной машине Windows Server 2016 +. |
| **Реорганизация документов FCI** | Документация по [экземплярам отказоустойчивого кластера с SQL Server на виртуальных машинах Azure](failover-cluster-instance-overview.md) была переписана и реорганизована для ясности. Мы разделили некоторое содержимое конфигурации, например рекомендации по [конфигурации кластера](hadr-cluster-best-practices.md), как подготовить [виртуальную машину для SQL Server FCI](failover-cluster-instance-prepare-vm.md)и как настроить [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Май 2020 г. 

| Изменения | Сведения |
| --- | --- |
| **Семейство SQL Azure** | SQL Server на виртуальных машинах Azure теперь является частью [семейства продуктов SQL Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md). Ознакомьтесь с нашим [новым знаком](../index.yml)! В продукте ничего не изменилось, но документация нацелена на упрощение принятия решений по продукту SQL Azure. | 


## <a name="january-2020"></a>Январь 2020 г.

| Изменения | Сведения |
| --- | --- |
| **Поддержка Azure для государственных организаций** | Теперь можно зарегистрировать SQL Server виртуальные машины с помощью поставщика ресурсов виртуальной машины SQL для виртуальных машин, размещенных в облаке [Azure для государственных организаций](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Изменения | Сведения |
 --- | --- |
| **Бесплатная реплика аварийного восстановления в Azure** | Если у вас есть действующее соглашение [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), вы можете разместить [свободный пассивный экземпляр](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) для аварийного восстановления в Azure для локального экземпляра SQL Server. | 
| **Массовая регистрация в поставщике ресурсов** | Теперь вы можете выполнять [полное регистрацию](sql-vm-resource-provider-bulk-register.md) SQL Server виртуальных машин с помощью поставщика ресурсов. | 
|**Конфигурация хранилища, оптимизированного для производительности** | Теперь при создании новой виртуальной машины SQL Server вы можете [полностью настроить конфигурацию хранилища](storage-configuration.md#new-vms). |
|**Общая папка ценовой категории "Премиум" для FCI** | Теперь можно создать экземпляр отказоустойчивого кластера с помощью [общего файлового ресурса](failover-cluster-instance-premium-file-share-manually-configure.md) уровня "Премиум" вместо исходного метода [Локальные дисковые пространства](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Выделенный узел Azure** | Вы можете запустить SQL Server виртуальную машину на [выделенном узле Azure](dedicated-host.md). | 
| **SQL Server миграцию виртуальной машины в другой регион** | Используйте службу Azure Site Recovery для [переноса виртуальных машин SQL Server из одного региона в другой](move-sql-vm-different-region.md). |
|  **Новые режимы установки SQL IaaS** | Теперь во избежание перезапуска службы SQL Server можно устанавливать расширение SQL Server IaaS в [упрощенном режиме](sql-server-iaas-agent-extension-automate-management.md).  |
| **Изменение выпуска SQL Server** | Теперь можно изменять [свойство выпуска](change-sql-server-edition.md) для виртуальной машины SQL Server. |
| **Изменения в поставщике ресурсов виртуальной машины SQL** | Вы можете [зарегистрировать виртуальную машину SQL Server в поставщике ресурсов виртуальной машины SQL](sql-vm-resource-provider-register.md), используя новые режимы SQL IaaS. Эта возможность включает [образы Windows Server 2008](sql-vm-resource-provider-register.md#management-modes).|
| **Образы с использованием собственной лицензии в рамках Преимущества гибридного использования Azure** | Образы с использованием собственной лицензии, развернутые из Azure Marketplace, позволяют менять [тип лицензии на модель с оплатой по мере использования](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Новые SQL Server управления ВМ в портал Azure** | Теперь на портале Azure доступна служба управления виртуальными машинами SQL Server. Дополнительные сведения см. в статье [Управление виртуальными машинами SQL Server на портале Azure](manage-sql-vm-portal.md).  | 
| **Расширенная поддержка для SQL Server 2008 и 2008 R2** | [Расширена поддержка](sql-server-2008-extend-end-of-support.md) для SQL Server 2008 и SQL Server 2008 R2 за счет переноса *как есть* на виртуальную машину Azure. | 
| **Возможность поддержки пользовательских образов** | Теперь можно установить [расширение SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) на пользовательские образы ос и SQL Server, которые обеспечивают ограниченную функциональность [гибкой лицензии](licensing-model-azure-hybrid-benefit-ahb-change.md). При регистрации пользовательского образа с помощью поставщика ресурсов виртуальной машины SQL укажите тип лицензии "AHUB". В противном случае регистрация завершится ошибкой. | 
| **Возможность поддержки именованных экземпляров** | Теперь можно использовать расширение [SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) с именованным экземпляром, если экземпляр по умолчанию был удален правильно. | 
| **Усовершенствование портала** | Для повышения удобства использования на портале Azure переработана процедура развертывания виртуальных машин SQL Server. Дополнительные сведения о развертывании виртуальной машины SQL Server см. в [кратком руководстве](sql-vm-create-portal-quickstart.md) и более подробном [практическом руководстве](create-sql-vm-portal.md).|
| **Улучшение портала** | Теперь на [портале Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider) можно изменять модель лицензирования для SQL Server виртуальной машины с оплатой по мере использования на собственную лицензию.|
| **Упрощение развертывания группы доступности на SQL Server виртуальной машине с помощью Azure CLI** | Теперь развертывание группы доступности на виртуальной машине SQL Server в Azure стало проще, чем когда-либо. [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) можно использовать для создания отказоустойчивого кластера Windows, внутреннего балансировщика нагрузки и прослушивателей групп доступности из командной строки. Дополнительные сведения см. в статье [использование Azure CLI для настройки группы доступности Always on для SQL Server на виртуальной машине Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Изменения | Сведения |
| --- | --- |
|  **Новый поставщик ресурсов для кластера SQL Server** | Новый поставщик ресурсов (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) определяет метаданные для отказоустойчивого кластера Windows. Присоединение виртуальной машины SQL Server к *SqlVirtualMachineGroups* обеспечивает начальную загрузку службы отказоустойчивого кластера Windows Server (WSFC) и присоединяет виртуальную машину к кластеру.  |
| **Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь можно создать отказоустойчивый кластер Windows, присоединить к нему SQL Server виртуальные машины, создать прослушиватель и настроить внутренний балансировщик нагрузки с помощью двух шаблонов быстрого запуска Azure. Дополнительные сведения см. в статье [Использование шаблонов быстрого запуска Azure для настройки группы доступности для SQL Server на виртуальной машине Azure](availability-group-quickstart-template-configure.md). | 
| **Автоматическая регистрация в поставщике ресурсов виртуальной машины SQL** | SQL Server виртуальные машины, развернутые после этого месяца, автоматически регистрируются новым поставщиком ресурсов виртуальной машины SQL. Виртуальные машины SQL Server, развернутые до этого месяца, по-прежнему необходимо регистрировать вручную. Дополнительные сведения см. в статье [Регистрация виртуальной машины SQL Server в Azure в поставщике ресурсов виртуальных машин SQL](sql-vm-resource-provider-register.md).|
|**Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов (Microsoft.SqlVirtualMachine) предоставляет возможности более эффективного управления виртуальной машиной SQL Server. Дополнительные сведения о регистрации виртуальных машин см. в статье [Регистрация виртуальной машины SQL Server в Azure в поставщике ресурсов виртуальных машин SQL](sql-vm-resource-provider-register.md). |
|**Переключение модели лицензирования** | Теперь с помощью Azure CLI или PowerShell вы можете переключаться между моделью оплаты за использование и моделью с использованием собственной лицензии для виртуальной машины SQL Server. Дополнительные сведения см. в разделе [Изменение модели лицензирования для виртуальной машины SQL Server в Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Дополнительные ресурсы

**Для виртуальных машин Windows**:

* [Обзор SQL Server на виртуальных машинах Windows](sql-server-on-azure-vm-iaas-what-is-overview.md).
* [Инициализация SQL Server на виртуальной машине Windows](create-sql-vm-portal.md)
* [Перенос базы данных в SQL Server на виртуальной машине Azure](migrate-to-vm-from-sql-server.md)
* [Высокая доступность и аварийное восстановление SQL Server на виртуальных машинах Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Рекомендации по оптимизации производительности SQL Server на виртуальных машинах Azure](performance-guidelines-best-practices.md)
* [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](application-patterns-development-strategies.md)

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).
* [Инициализация SQL Server на виртуальной машине Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../linux/frequently-asked-questions-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).

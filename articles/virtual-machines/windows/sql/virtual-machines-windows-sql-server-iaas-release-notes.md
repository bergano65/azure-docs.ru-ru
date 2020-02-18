---
title: Изменения документации для SQL Server на виртуальных машинах Azure | Документация Майкрософт
description: Узнайте о новых возможностях и улучшениях для SQL Server на виртуальной машине Azure.
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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201651"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Изменения документации для SQL Server на виртуальных машинах Azure

Azure позволяет развернуть виртуальную машину с помощью образа встроенной SQL Server. В этой статье перечислены изменения документации, связанные с новыми функциями и улучшениями в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Январь 2020 г.

| Изменения | Подробности |
| --- | --- |
| **Поддержка Azure для государственных организаций** | Теперь можно зарегистрировать SQL Server виртуальные машины с помощью поставщика ресурсов виртуальной машины SQL для виртуальных машин, размещенных в облаке [Azure для государственных организаций](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Изменения | Подробности |
 --- | --- |
| **Бесплатная реплика аварийного восстановления в Azure** | Вы можете разместить [бесплатный пассивный экземпляр](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) для аварийного восстановления в Azure для локального экземпляра SQL Server, если у вас есть [Программа Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Регистрация небольшого поставщика ресурсов** | Теперь вы можете выполнять [полное регистрацию](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) виртуальных машин SQL с помощью поставщика ресурсов. | 
|**Конфигурация хранилища, оптимизированного для производительности** | Теперь можно [полностью настроить конфигурацию хранилища](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) при создании новой SQL Server виртуальной машины. |
|**Файловый ресурс Premium для FCI** | Теперь можно создать экземпляр отказоустойчивого кластера с помощью [общего файлового ресурса](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) уровня "Премиум" вместо исходного метода [Локальные дисковые пространства](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Выделенный узел Azure** | Вы можете запустить SQL Serverную виртуальную машину на [выделенном узле Azure](virtual-machines-windows-sql-dedicated-host.md). | 
| **Перемещение виртуальной машины SQL в другой регион** | Используйте Azure Site Recovery для [переноса виртуальной машины SQL Server из одного региона в другой](virtual-machines-windows-sql-move-different-region.md). |
|  **Новые режимы установки IaaS SQL** | Теперь можно установить расширение SQL Server IaaS в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md) , чтобы избежать перезапуска службы SQL Server.  |
| **Изменение выпуска SQL Server** | Теперь можно изменить [свойство Edition](virtual-machines-windows-sql-change-edition.md) для виртуальной машины SQL Server. |
| **Изменения в поставщике ресурсов виртуальной машины SQL** | Вы можете [зарегистрировать SQL Server виртуальную машину с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , используя новые режимы SQL IaaS. Эта возможность включает в себя образы [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) .|
| **Использование собственных образов лицензий с помощью Преимущество гибридного использования Azure** | Образы с собственными лицензиями, развернутые из Azure Marketplace, теперь могут переключить [тип лицензии на "Оплата по мере использования"](virtual-machines-windows-sql-ahb.md#remarks).| 
| **Новые SQL Server управления виртуальными машинами в портал Azure** | Теперь существует способ управления SQL Server виртуальной машиной в портал Azure. Дополнительные сведения см. [в разделе Управление виртуальными машинами SQL Server в портал Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| **Расширенная поддержка для SQL Server 2008/2008 R2** | [Расширьте поддержку](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 и SQL Server 2008 R2, переполнив миграцию *как есть* на виртуальную машину Azure. | 
| **Поддержка пользовательских образов** | Теперь можно установить [расширение SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) в пользовательские ОС и образы SQL, которые обеспечивают ограниченную функциональность [гибкой лицензии](virtual-machines-windows-sql-ahb.md). При регистрации пользовательского образа с помощью поставщика ресурсов SQL укажите тип лицензии "AHUB". В противном случае регистрация завершится ошибкой. | 
| **Поддержка именованных экземпляров** | Теперь можно использовать [расширение IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) с именованным экземпляром, если экземпляр по умолчанию был удален правильно. | 
| **Улучшение портала** | Портал Azure возможности развертывания виртуальной машины SQL Server были пересмотрены для повышения удобства использования. Дополнительные сведения см. [в кратком](quickstart-sql-vm-create-portal.md) руководстве и подробное [руководство](virtual-machines-windows-portal-sql-server-provision.md) по развертыванию SQL Server виртуальной машины.|
| **Улучшение портала** | Теперь можно изменить модель лицензирования для SQL Server виртуальной машины с оплатой по мере использования на собственную лицензию с помощью [портал Azure](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Упрощение развертывания группы доступности с помощью интерфейса командной строки Azure SQL Server VM** | Теперь развертывание группы доступности на SQL Server виртуальной машине в Azure стало проще, чем когда-либо. Вы можете использовать [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) для создания отказоустойчивого кластера Windows, внутреннего балансировщика нагрузки и прослушивателей группы доступности из командной строки. Дополнительные сведения см. в статье [Использование Azure SQL Server VM CLI для настройки группы доступности Always on для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Изменения | Подробности |
| --- | --- |
|  **Новый поставщик ресурсов для кластера SQL Server** | Новый поставщик ресурсов (Microsoft. Склвиртуалмачине/Склвиртуалмачинеграупс) определяет метаданные отказоустойчивого кластера Windows. Присоединение SQL Serverной виртуальной машины к *склвиртуалмачинеграупс* загрузкой службы отказоустойчивого кластера Windows Server (WSFC) и присоединение виртуальной машины к кластеру.  |
| **Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь можно создать отказоустойчивый кластер Windows, присоединить к нему SQL Server виртуальные машины, создать прослушиватель и настроить внутренний балансировщик нагрузки с помощью двух шаблонов быстрого запуска Azure. Дополнительные сведения см. [в статье Использование шаблонов быстрого запуска Azure для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Автоматическая регистрация в поставщике ресурсов виртуальной машины SQL** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. SQL Server виртуальные машины, развернутые до этого месяца, по-прежнему необходимо зарегистрировать вручную. Дополнительные сведения см. в статье [регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов (Microsoft. Склвиртуалмачине) обеспечивает улучшенное управление виртуальными машинами SQL Server. Дополнительные сведения о регистрации виртуальных машин см. в статье [регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Переключение модели лицензирования** | Теперь вы можете переключаться между моделями оплаты за использование и собственными лицензиями для SQL Server виртуальной машины с помощью Azure CLI или PowerShell. Дополнительные сведения см. в статье [изменение модели лицензирования для SQL Server виртуальной машины в Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Дополнительные ресурсы

**Для виртуальных машин Windows**:

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Windows](virtual-machines-windows-portal-sql-server-provision.md).
* [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
* [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Рекомендации по повышению производительности SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
* [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
* [Подготавливает виртуальную машину SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../../linux/sql/sql-server-linux-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).

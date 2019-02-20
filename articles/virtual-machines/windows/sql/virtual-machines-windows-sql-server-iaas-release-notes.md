---
title: Заметки о выпуске SQL Server на виртуальных машинах в Azure | Документация Майкрософт
description: Узнайте о новых функциях и улучшения SQL Server на виртуальных машинах Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/5/2019
ms.openlocfilehash: b59ac4798260381c11ab22adb7358ff63e5e1d77
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245428"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Заметки о выпуске SQL Server на виртуальных машинах Azure

Azure позволяет развертывать виртуальные машины со встроенным образом SQL Server. В этой статье перечислены новые функции и улучшения в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). В ней также указаны заметные обновления содержимого, которые не связаны напрямую с выпуском, но опубликованы в тот же интервал времени. Улучшения других служб Azure см. в статье [Обновления Azure](https://azure.microsoft.com/updates)

## <a name="december-2018"></a>Декабрь 2018 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| **Новый поставщик ресурсов группы кластера SQL** | Новый поставщик ресурсов (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), который определяет метаданные для отказоустойчивого кластера Windows. Присоединение виртуальной машины SQL Server к *SqlVirtualMachineGroups* обеспечивает начальную загрузку службы отказоустойчивого кластера Windows и присоединяет виртуальную машину к кластеру.  |
|**Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь с помощью двух шаблонов быстрого запуска Azure можно создать отказоустойчивый кластер Windows, присоединить к нему виртуальные машины SQL Server, создать прослушивателя, а также настроить внутреннюю подсистему балансировки нагрузки. Дополнительные сведения см. в статье [Create WSFC, listener, and configure ILB for an Always On availability group on a SQL Server VM with Azure Quickstart Template](virtual-machines-windows-sql-availability-group-quickstart-template.md) (Создание WSFC и прослушивателя и настройка внутреннего балансировщика нагрузки для группы доступности Always On с помощью шаблона быстрого запуска Azure). | 
| **Автоматическая регистрация поставщика ресурсов виртуальной машины SQL** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. Виртуальные машины SQL Server, развернутые до этого месяца, по-прежнему необходимо зарегистрировать вручную. Дополнительные сведения см. в разделе [Регистрация существующей виртуальной машины SQL в новом поставщике ресурсов](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
|Нет | |
| | |

## <a name="november-2018"></a>Ноябрь 2018 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| **Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов для виртуальных машин SQL Server (Microsoft.SqlVirtualMachine), предоставляющий возможности более эффективного управления виртуальной машиной SQL Server. См. дополнительные сведения о [регистрации существующих виртуальных машин SQL с использованием нового поставщика ресурсов](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Переключение модели лицензирования** |Теперь вы можете переключаться между моделью оплаты за использование и моделью с использованием собственной лицензии для виртуальной машины SQL с помощью Azure CLI или PowerShell. Дополнительные сведения см. в статье об [изменение модели лицензирования для виртуальной машины SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Улучшения документации | Сведения |
| --- | --- |
|Нет | |
| | |

## <a name="additional-resources"></a>Дополнительные ресурсы

**Для виртуальных машин Windows**:

* [Приступая к работе с SQL Server на виртуальных машинах Azure (Windows)](virtual-machines-windows-sql-server-iaas-overview.md)
* [Подготовка виртуальной машины SQL Server на базе Windows](virtual-machines-windows-portal-sql-server-provision.md).
* [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
* [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
* [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](../../linux/sql/provision-sql-server-linux-virtual-machine.md).
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../../linux/sql/sql-server-linux-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
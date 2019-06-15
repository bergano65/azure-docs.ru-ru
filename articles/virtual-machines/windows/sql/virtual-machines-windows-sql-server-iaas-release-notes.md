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
ms.date: 2/13/2019
ms.openlocfilehash: ee3aeb9f44d1b98d6307c6a72d1e4786ea1ec664
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076902"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Заметки о выпуске SQL Server на виртуальных машинах Azure

Azure позволяет развертывать виртуальные машины со встроенным образом SQL Server. В этой статье перечислены новые функции и улучшения в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). В ней также указаны заметные обновления содержимого, которые не связаны напрямую с выпуском, но опубликованы в тот же интервал времени. Улучшения других служб Azure см. в статье [Обновления Azure](https://azure.microsoft.com/updates)

## <a name="may-2019"></a>Май 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| **Новое управление виртуальной Машины SQL на портале Azure** | Теперь есть новый способ управления виртуальной Машине SQL Server на портале Azure. Дополнительные сведения см. в разделе [управление виртуальной Машины SQL Server на портале Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Документация | Сведения |
| --- | --- |
| **Порталу управления новой виртуальной Машины SQL** | Около десяти статьи были обновлены, чтобы новый интерфейс портала управления виртуальной Машины SQL. | 
| &nbsp; | &nbsp; |


## <a name="april-2019"></a>Апрель 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Сведения |
| --- | --- |
| **Расширения поддержки SQL Server 2008/2008 R2** | [Расширения поддержки](virtual-machines-windows-sql-server-2008-eos-extend-support.md) для SQL Server 2008 и SQL Server 2008 R2 путем переноса *как-является* к виртуальной Машине Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Марта 2019 г.

| Улучшения службы | Сведения |
| --- | --- |
| **Поддержка пользовательского образа** | Теперь вы можете установить [расширения SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) для пользовательских образов операционной системы и SQL, который предоставляет ограниченные функциональные возможности [гибкие лицензирования](virtual-machines-windows-sql-ahb.md). При регистрации пользовательского образа с помощью поставщика ресурсов SQL, указать тип лицензии в качестве «AHUB» как в противном случае Регистрация завершится ошибкой.  | 
| **Возможности поддержки именованного экземпляра** | Могут теперь использовать [расширения SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) с именованным экземпляром, если экземпляр по умолчанию должным образом удален. | 
| **Расширение портала** | Чтобы повысить удобство использования кардинально изменена интерфейс портала Azure для развертывания виртуальной Машины SQL Server. Дополнительные сведения см. краткое описание [быстрого запуска](quickstart-sql-vm-create-portal.md) более детальная проверка [руководства](virtual-machines-windows-portal-sql-server-provision.md) руководство по развертыванию виртуальной Машины SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Февраль 2019 г.

| Улучшения службы | Сведения |
| --- | --- |
| **Улучшения портала** | Теперь имеется возможность изменить модель лицензирования для SQL Server виртуальной Машины с оплатой по мере использования использовать владельцем лицензии с помощью [портала Azure](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Упрощение развертывания группы Доступности с помощью интерфейса командной строки виртуальной Машины SQL Azure** | Теперь стало проще, чем когда-либо чтобы развернуть группу доступности для виртуальной Машины SQL Server в Azure. [Azure CLI для виртуальной Машины SQL](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) позволяет создавать прослушиватель WSFC, внутренней подсистемы балансировки Нагрузки и группы Доступности, все из командной строки и в рекордные сроки! Дополнительные сведения см. в разделе [использовании CLI виртуальной Машины SQL Azure для настройки группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Декабрь 2018 г.

| Улучшения службы | Сведения |
| --- | --- |
| **Новый поставщик ресурсов группы кластера SQL** | Новый поставщик ресурсов (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), который определяет метаданные для отказоустойчивого кластера Windows. Присоединение виртуальной машины SQL Server к *SqlVirtualMachineGroups* обеспечивает начальную загрузку службы отказоустойчивого кластера Windows и присоединяет виртуальную машину к кластеру.  |
|**Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь с помощью двух шаблонов быстрого запуска Azure можно создать отказоустойчивый кластер Windows, присоединить к нему виртуальные машины SQL Server, создать прослушивателя, а также настроить внутреннюю подсистему балансировки нагрузки. Дополнительные сведения см. в разделе [использовать шаблон быстрого запуска Azure для настройки группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Автоматическая регистрация поставщика ресурсов виртуальной машины SQL** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. Виртуальные машины SQL Server, развернутые до этого месяца, по-прежнему необходимо зарегистрировать вручную. Дополнительные сведения см. в разделе [зарегистрировать существующей виртуальной Машины SQL с поставщиком ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Ноябрь 2018 г.

| Улучшения службы | Сведения |
| --- | --- |
| **Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов для виртуальных машин SQL Server (Microsoft.SqlVirtualMachine), предоставляющий возможности более эффективного управления виртуальной машиной SQL Server. См. дополнительные сведения о [регистрации существующих виртуальных машин SQL с использованием нового поставщика ресурсов](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Переключение модели лицензирования** |Теперь вы можете переключаться между моделью оплаты за использование и моделью с использованием собственной лицензии для виртуальной машины SQL с помощью Azure CLI или PowerShell. Дополнительные сведения см. в статье об [изменение модели лицензирования для виртуальной машины SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


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

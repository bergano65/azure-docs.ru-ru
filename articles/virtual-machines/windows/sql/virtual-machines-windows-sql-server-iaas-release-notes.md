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
ms.openlocfilehash: e7dea69b507117ba8a3765b5e9bc1fad46be4c53
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444208"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Заметки о выпуске SQL Server на виртуальных машинах Azure

Azure позволяет развертывать виртуальные машины со встроенным образом SQL Server. В этой статье перечислены новые функции и улучшения в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). В ней также указаны заметные обновления содержимого, которые не связаны напрямую с выпуском, но опубликованы в тот же интервал времени. Улучшения других служб Azure см. в статье [Обновления Azure](https://azure.microsoft.com/updates)


## <a name="june-2019"></a>Июнь 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Подробнее |
| --- | --- |
| **Новые режимы установки IaaS SQL** | Теперь можно установить расширение IaaS SQL в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md) , чтобы избежать перезапуска службы SQL Server.  |
| **Изменение выпуска SQL Server** | Теперь можно изменить [свойство Edition](virtual-machines-windows-sql-change-edition.md) для виртуальной машины SQL Server. |
| **Изменения в RP виртуальной машины SQL** | Вы можете [зарегистрировать SQL Server виртуальную машину с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) — даже [образов Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) — с помощью новых режимов IaaS SQL. |
| **BYOL образы с помощью AHUB** | Образы BYOL, развернутые из Marketplace, теперь могут переключить [тип лицензии на "PAYG"](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Май 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Подробнее |
| --- | --- |
| **Новое Управление виртуальными машинами SQL в портал Azure** | Теперь существует новый способ управления SQL Server виртуальной машиной в портал Azure. Дополнительные сведения см. [в разделе Управление виртуальной машиной SQL Server в портал Azure](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Улучшения документации

| Документация | Подробнее |
| --- | --- |
| **Управление новым порталом виртуальных машин SQL** | Около десятка статей были обновлены с учетом новых возможностей портала управления виртуальной машиной SQL. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Апрель 2019 г.

### <a name="service-improvements"></a>Улучшения службы

| Улучшения службы | Подробнее |
| --- | --- |
| **Расширение поддержки для SQL Server 2008 и 2008R2** | [Расширьте поддержку](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 и SQL Server 2008 R2, переполнив миграцию " *как есть* " на виртуальную машину Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Март 2019

| Улучшения службы | Подробнее |
| --- | --- |
| **Поддержка пользовательских образов** | Теперь вы можете установить [расширение IaaS для SQL](virtual-machines-windows-sql-server-agent-extension.md#installation) в пользовательские ОС и образы SQL, которые предоставляют ограниченную функциональность [гибкой лицензии](virtual-machines-windows-sql-ahb.md). При регистрации пользовательского образа с помощью поставщика ресурсов SQL укажите тип лицензии "AHUB", в противном случае регистрация завершится ошибкой. | 
| **Поддержка именованных экземпляров** | Теперь можно использовать [расширение SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) с именованным экземпляром, если экземпляр по умолчанию был удален правильно. | 
| **Улучшение портала** | Портал Azure возможности развертывания виртуальной машины SQL Server были пересмотрены для повышения удобства использования. Дополнительные сведения см. в кратком [руководстве и](quickstart-sql-vm-create-portal.md) подробное [руководство по](virtual-machines-windows-portal-sql-server-provision.md) развертыванию SQL Server виртуальной машины.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Февраль 2019 г.

| Улучшения службы | Подробнее |
| --- | --- |
| **Улучшение портала** | Теперь можно изменить модель лицензирования для SQL Server виртуальной машины с оплатой по мере использования на собственную лицензию с помощью [портал Azure](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Упрощение развертывания группы доступности с помощью CLI виртуальной машины SQL Azure** | Теперь развертывание группы доступности на SQL Server виртуальной машине в Azure стало проще, чем когда-либо. [CLI виртуальной машины SQL Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) позволяет создавать прослушиватели WSFC, ilB и AG из командной строки и во время записи. Дополнительные сведения см. [в статье Использование интерфейса командной строки Azure SQL VM для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Декабрь 2018 г.

| Улучшения службы | Подробнее |
| --- | --- |
| **Новый поставщик ресурсов группы кластера SQL** | Новый поставщик ресурсов (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), который определяет метаданные для отказоустойчивого кластера Windows. Присоединение виртуальной машины SQL Server к *SqlVirtualMachineGroups* обеспечивает начальную загрузку службы отказоустойчивого кластера Windows и присоединяет виртуальную машину к кластеру.  |
|**Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь с помощью двух шаблонов быстрого запуска Azure можно создать отказоустойчивый кластер Windows, присоединить к нему виртуальные машины SQL Server, создать прослушивателя, а также настроить внутреннюю подсистему балансировки нагрузки. Дополнительные сведения см. [в статье Использование шаблона быстрого запуска Azure для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Автоматическая регистрация поставщика ресурсов виртуальной машины SQL** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. Виртуальные машины SQL Server, развернутые до этого месяца, по-прежнему необходимо зарегистрировать вручную. Дополнительные сведения см. в статье [Регистрация существующей виртуальной машины SQL с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Ноябрь 2018 г.

| Улучшения службы | Подробнее |
| --- | --- |
| **Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов для виртуальных машин SQL Server (Microsoft.SqlVirtualMachine), предоставляющий возможности более эффективного управления виртуальной машиной SQL Server. См. дополнительные сведения о [регистрации существующих виртуальных машин SQL с использованием нового поставщика ресурсов](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Переключение модели лицензирования** | Теперь вы можете переключаться между моделью оплаты за использование и моделью с использованием собственной лицензии для виртуальной машины SQL с помощью Azure CLI или PowerShell. Дополнительные сведения см. в статье об [изменение модели лицензирования для виртуальной машины SQL](virtual-machines-windows-sql-ahb.md). | 
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

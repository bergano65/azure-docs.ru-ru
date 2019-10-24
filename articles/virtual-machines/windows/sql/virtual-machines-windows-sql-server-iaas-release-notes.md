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
ms.date: 08/01/2019
ms.openlocfilehash: 599dd2b44f934bb864acfa9ffe8d03fa0e8bb850
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757466"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Изменения документации для SQL Server на виртуальных машинах Azure

Azure позволяет развернуть виртуальную машину с помощью образа встроенной SQL Server. В этой статье перечислены изменения документации, связанные с новыми функциями и улучшениями в последних выпусках [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>Октябрь 2019 г.

| изменения | Сведения |
| --- | --- |
| **Конфигурация хранилища, оптимизированного для производительности** | Теперь можно [полностью настроить конфигурацию хранилища](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) при создании новой SQL Server виртуальной машины. |
| **Файловый ресурс Premium для FCI** | Теперь можно создать экземпляр отказоустойчивого кластера с помощью [общего файлового ресурса](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) уровня "Премиум" вместо исходного метода [Локальные дисковые пространства](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Август 2019 г.

| изменения | Сведения |
| --- | --- |
| **Выделенный узел Azure** | Вы можете запустить SQL Serverную виртуальную машину на [выделенном узле Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Июль 2019 г.


| изменения | Сведения |
| --- | --- |
| **Перемещение виртуальной машины SQL в другой регион** | Используйте Azure Site Recovery для [переноса виртуальной машины SQL Server из одного региона в другой](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Июнь 2019 г.


| изменения | Сведения |
| --- | --- |
| **Новые режимы установки IaaS SQL** | Теперь можно установить расширение SQL Server IaaS в [упрощенном режиме](virtual-machines-windows-sql-server-agent-extension.md) , чтобы избежать перезапуска службы SQL Server.  |
| **Изменение выпуска SQL Server** | Теперь можно изменить [свойство Edition](virtual-machines-windows-sql-change-edition.md) для виртуальной машины SQL Server. |
| **Изменения в поставщике ресурсов виртуальной машины SQL** | Вы можете [зарегистрировать SQL Server виртуальную машину с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , используя новые режимы SQL IaaS. Эта возможность включает [образы Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).|
| **Использование собственных образов лицензий с помощью Преимущество гибридного использования Azure** | Образы с собственными лицензиями, развернутые из Azure Marketplace, теперь могут переключить [тип лицензии на "Оплата по мере использования"](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Май 2019 г.

| изменения | Сведения |
| --- | --- |
| **Новые SQL Server управления виртуальными машинами в портал Azure** | Теперь существует способ управления SQL Server виртуальной машиной в портал Azure. Дополнительные сведения см. [в разделе Управление виртуальными машинами SQL Server в портал Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>Апрель 2019 г.

| изменения | Сведения |
| --- | --- |
| **Расширенная поддержка для SQL Server 2008/2008 R2** | [Расширьте поддержку](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 и SQL Server 2008 R2, переполнив миграцию *как есть* на виртуальную машину Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Март 2019 г.

| изменения | Сведения |
| --- | --- |
| **Поддержка пользовательских образов** | Теперь можно установить [расширение SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) в пользовательские ОС и образы SQL, которые обеспечивают ограниченную функциональность [гибкой лицензии](virtual-machines-windows-sql-ahb.md). При регистрации пользовательского образа с помощью поставщика ресурсов SQL укажите тип лицензии "AHUB". В противном случае регистрация завершится ошибкой. | 
| **Поддержка именованных экземпляров** | Теперь можно использовать [расширение IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) с именованным экземпляром, если экземпляр по умолчанию был удален правильно. | 
| **Улучшение портала** | Портал Azure возможности развертывания виртуальной машины SQL Server были пересмотрены для повышения удобства использования. Дополнительные сведения см. [в кратком](quickstart-sql-vm-create-portal.md) руководстве и подробное [руководство](virtual-machines-windows-portal-sql-server-provision.md) по развертыванию SQL Server виртуальной машины.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Февраль 2019 г.

| изменения | Сведения |
| --- | --- |
| **Улучшение портала** | Теперь можно изменить модель лицензирования для SQL Server виртуальной машины с оплатой по мере использования на собственную лицензию с помощью [портал Azure](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Упрощение развертывания группы доступности с помощью интерфейса командной строки Azure SQL Server VM** | Теперь развертывание группы доступности на SQL Server виртуальной машине в Azure стало проще, чем когда-либо. Вы можете использовать [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) для создания отказоустойчивого кластера Windows, внутреннего балансировщика нагрузки и прослушивателей группы доступности из командной строки. Дополнительные сведения см. в статье [Использование Azure SQL Server VM CLI для настройки группы доступности Always on для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>Декабрь 2018 г.

| изменения | Сведения |
| --- | --- |
| **Новый поставщик ресурсов для кластера SQL Server** | Новый поставщик ресурсов (Microsoft. Склвиртуалмачине/Склвиртуалмачинеграупс) определяет метаданные отказоустойчивого кластера Windows. Присоединение SQL Serverной виртуальной машины к *склвиртуалмачинеграупс* загрузкой службы отказоустойчивого кластера Windows Server (WSFC) и присоединение виртуальной машины к кластеру.  |
|**Автоматическая настройка развертывания группы доступности с помощью шаблонов быстрого запуска Azure** |Теперь можно создать отказоустойчивый кластер Windows, присоединить к нему SQL Server виртуальные машины, создать прослушиватель и настроить внутренний балансировщик нагрузки с помощью двух шаблонов быстрого запуска Azure. Дополнительные сведения см. [в статье Использование шаблонов быстрого запуска Azure для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Автоматическая регистрация в поставщике ресурсов виртуальной машины SQL** | Виртуальные машины SQL Server, развернутые после этого месяца, автоматически регистрируются с новым поставщиком ресурсов SQL Server. SQL Server виртуальные машины, развернутые до этого месяца, по-прежнему необходимо зарегистрировать вручную. Дополнительные сведения см. в статье [регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>Ноябрь 2018 г.

| изменения | Сведения |
| --- | --- |
| **Новый поставщик ресурсов виртуальной машины SQL** |  Новый поставщик ресурсов (Microsoft. Склвиртуалмачине) обеспечивает улучшенное управление виртуальными машинами SQL Server. Дополнительные сведения о регистрации виртуальных машин см. в статье [регистрация SQL Server виртуальной машины в Azure с помощью поставщика ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Переключение модели лицензирования** | Теперь вы можете переключаться между моделями оплаты за использование и собственными лицензиями для SQL Server виртуальной машины с помощью Azure CLI или PowerShell. Дополнительные сведения см. в статье [изменение модели лицензирования для SQL Server виртуальной машины в Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Дополнительные ресурсы

**Виртуальные машины Windows**:

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Windows на портале Azure](virtual-machines-windows-portal-sql-server-provision.md)
* [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
* [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Рекомендации по повышению производительности SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
* [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Виртуальные машины Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
* [Подготавливает виртуальную машину SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../../linux/sql/sql-server-linux-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

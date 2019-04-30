---
title: Сетевые топологии для переноса Управляемого экземпляра базы данных SQL Azure с помощью Azure Database Migration Service | Документы Майкрософт
description: Сведения об исходной и целевой конфигурациях для Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61097842"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Сетевые топологии для переноса Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service
Эта статья описывает различные сетевые топологии, поддерживаемые Azure Database Migration Service для комплексного переноса Управляемого экземпляра базы данных SQL Azure из локальных систем SQL Server.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Управляемый экземпляр Базы данных SQL Azure, настроенный для гибридных рабочих нагрузок 
Используйте эту топологию, если Управляемый экземпляр Базы данных SQL Azure подключен к локальной сети. Такой подход упрощает сетевую маршрутизацию и максимально повышает пропускную способность во время миграции.

![Сетевая топология для гибридных рабочих нагрузок](media/resource-network-topologies/hybrid-workloads.png)

**Требования**
- В этом сценарии создается Управляемый экземпляр Базы данных SQL Azure и экземпляр Azure Database Migration Service в одной виртуальной сети Azure, но в разных подсетях.  
- Виртуальная сеть, используемая в этом сценарии, также подключена к локальной сети с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Управляемый экземпляр Базы данных SQL Azure, изолированный от локальной сети
Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:
- Управляемый экземпляр Базы данных SQL Azure изолирован от локального подключения, но экземпляр Azure Database Migration Service подключен к локальной сети.
- Применяются политики управления доступом на основе ролей, и вам необходимо ограничить доступ пользователей к той же подписке, в которой размещен Управляемый экземпляр Базы данных SQL Azure.
- Виртуальные сети, используемые для Управляемого экземпляра Базы данных SQL Azure и для Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для Управляемого экземпляра Базы данных SQL Azure, изолированного от локальной сети](media/resource-network-topologies/mi-isolated-workload.png)

**Требования**
- Виртуальная сеть, которую использует Azure Database Migration Service для этого сценария, также должна быть подключена к локальной сети с помощью https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Вы можете настроить [пиринг между виртуальными сетями](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview), используемыми для Управляемого экземпляра базы данных SQL Azure и для Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Миграция между облаками: общая виртуальная сеть

Используйте эту топологию, если исходный SQL Server размещается на виртуальной машине Azure и использует ту же виртуальную сеть, что и Управляемый экземпляр Базы данных SQL Azure и Azure Database Migration Service.

![Сетевая топология для миграции между облаками с общей виртуальной сетью](media/resource-network-topologies/cloud-to-cloud.png)

**Требования**
- Дополнительных требований нет.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Миграция между облаками: изолированная виртуальная сеть

Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:
- Управляемый экземпляр Базы данных SQL Azure подготавливается в изолированной виртуальной сети.
- Применяются политики управления доступом на основе ролей, и вам необходимо ограничить доступ пользователей к той же подписке, в которой размещен Управляемый экземпляр Базы данных SQL Azure.
- Виртуальные сети, используемые для Управляемого экземпляра Базы данных SQL Azure и для Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для миграции между облаками с изолированной виртуальной сетью](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Требования**
- Вы можете настроить [пиринг между виртуальными сетями](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview), используемыми для Управляемого экземпляра базы данных SQL Azure и для Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Правила безопасности для входящего трафика

| **ИМЯ**   | **ПОРТ** | **ПРОТОКОЛ** | **ИСТОЧНИК** | **НАЗНАЧЕНИЕ** | **ДЕЙСТВИЕ** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Все      | Все          | Подсеть DMS | Все             | Allow      |

## <a name="outbound-security-rules"></a>Правила безопасности для исходящего трафика

| **ИМЯ**                  | **ПОРТ**                                              | **ПРОТОКОЛ** | **ИСТОЧНИК** | **НАЗНАЧЕНИЕ**           | **ДЕЙСТВИЕ** | **Причина для правила**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| управление                | 443,9354                                              | TCP          | Все        | Все                       | Allow      | Управляющее взаимодействие по служебной шине с Хранилищем BLOB-объектов Azure. <br/>(Если включен пиринг Майкрософт, это правило может не понадобиться.)                                                             |
| Диагностика               | 12000                                                 | TCP          | Все        | Все                       | Allow      | DMS использует это правило для сбора диагностических сведений для устранения неполадок.                                                                                                                      |
| Исходный SQL Server         | 1433 (или порт TCP IP, который прослушивает SQL Server) | TCP          | Все        | локальное адресное пространство; | Allow      | Возможность подключения к исходному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                                       |
| Именованный экземпляр SQL Server | 1434                                                  | UDP          | Все        | локальное адресное пространство; | Allow      | Возможность подключения к исходному именованному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                        |
| Общий доступ по SMB                 | 445                                                   | TCP          | Все        | локальное адресное пространство; | Allow      | Общий сетевой доступ по SMB в DMS для хранения файлов резервных копий базы данных с целью переноса в управляемый экземпляр Базы данных SQL Azure и SQL Server на виртуальной машине Azure. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.) |
| DMS_subnet                | Все                                                   | Все          | Все        | DMS_subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>См. также
- [Migrate SQL Server to Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) (Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure)
- [Предварительные требования для использования службы Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Создание виртуальной сети с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Дальнейшие действия
- Общие сведения о службе Azure Database Migration Service см. в статье [Что такое Azure Database Migration Service?](dms-overview.md).
- Актуальные сведения о доступности Azure Database Migration Service в регионах см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).
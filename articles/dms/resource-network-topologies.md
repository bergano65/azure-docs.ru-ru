---
title: Сетевые топологии для миграций SQL Управляемый экземпляр
titleSuffix: Azure Database Migration Service
description: Изучите исходную и целевую конфигурации миграции Azure SQL Управляемый экземпляр с помощью Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 5839de1fde8e4a4d5e661d232ae91099a9483bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291577"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Сетевые топологии для миграции Управляемый экземпляр Azure SQL с помощью Azure Database Migration Service

В этой статье обсуждаются различные сетевые топологии, с которыми Azure Database Migration Service может работать для обеспечения полной миграции с серверов SQL Server на Управляемый экземпляр SQL Azure.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Управляемый экземпляр Azure SQL, настроенные для гибридных рабочих нагрузок 

Используйте эту топологию, если Управляемый экземпляр Azure SQL подключена к локальной сети. Такой подход упрощает сетевую маршрутизацию и максимально повышает пропускную способность во время миграции.

![Сетевая топология для гибридных рабочих нагрузок](media/resource-network-topologies/hybrid-workloads.png)

**Requirements**

- В этом сценарии Управляемый экземпляр SQL и экземпляр Azure Database Migration Service создаются в одном виртуальная сеть Microsoft Azure, но они используют разные подсети.  
- Виртуальная сеть, используемая в этом сценарии, также подключается к локальной сети с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Управляемый экземпляр SQL изолированы от локальной сети

Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:

- Управляемый экземпляр SQL изолированы от локальных подключений, но ваш экземпляр Azure Database Migration Service подключен к локальной сети.
- Если применяются политики управления доступом на основе ролей Azure (Azure RBAC) и необходимо ограничить доступ пользователей к той же подписке, где размещается Управляемый экземпляр SQL.
- Виртуальные сети, используемые для Управляемый экземпляр SQL и Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для Управляемого экземпляра Базы данных SQL Azure, изолированного от локальной сети](media/resource-network-topologies/mi-isolated-workload.png)

**Requirements**

- Виртуальная сеть, которая Azure Database Migration Service используется для этого сценария, также должна быть подключена к локальной сети с помощью ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN-подключения](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)).
- Настройте [пиринг сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) виртуальной сети между виртуальной сетью, используемой для SQL Управляемый экземпляр и Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Миграция из облака в облако: общая виртуальная сеть

Используйте эту топологию, если исходный SQL Server размещается на виртуальной машине Azure и использует ту же виртуальную сеть с SQL Управляемый экземпляр и Azure Database Migration Service.

![Топология сети для миграции из облака в облако с помощью общей виртуальной сети](media/resource-network-topologies/cloud-to-cloud.png)

**Requirements**

- Дополнительных требований нет.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Миграция облака в облако: изолированная виртуальная сеть

Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:

- Управляемый экземпляр SQL подготавливается в изолированной виртуальной сети.
- Если применяются политики управления доступом на основе ролей Azure (Azure RBAC) и необходимо ограничить доступ пользователей к той же подписке, где размещается Управляемый экземпляр SQL.
- Виртуальные сети, используемые для SQL Управляемый экземпляр и Azure Database Migration Service, находятся в разных подписках.

![Топология сети для миграции из облака в облако с помощью изолированной виртуальной сети](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requirements**

- Настройте [пиринг сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) виртуальной сети между виртуальной сетью, используемой для SQL Управляемый экземпляр и Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Правила безопасности для входящего трафика

| **ИМЯ**   | **ПОРТУ** | **СМ** | **ИСТОЧНИКА** | **МЕСТОНАЗНАЧЕНИЕ** | **ДЕЙСТВИЕ** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Любой      | Любой          | Подсеть DMS | Любой             | Allow      |

## <a name="outbound-security-rules"></a>Правила безопасности для исходящего трафика

| **ИМЯ**                  | **ПОРТУ**                                              | **СМ** | **ИСТОЧНИКА** | **МЕСТОНАЗНАЧЕНИЕ**           | **ДЕЙСТВИЕ** | **Причина для правила**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| управление                | 443,9354                                              | TCP          | Любой        | Любой                       | Allow      | Взаимодействие плоскости управления через служебную шину и хранилище BLOB-объектов Azure. <br/>(Если включен пиринг Майкрософт, это правило может не понадобиться.)                                                             |
| Диагностика               | 12000                                                 | TCP          | Любой        | Любой                       | Allow      | DMS использует это правило для сбора диагностических сведений для устранения неполадок.                                                                                                                      |
| Исходный SQL Server         | 1433 (или порт TCP IP, который прослушивает SQL Server) | TCP          | Любой        | локальное адресное пространство; | Allow      | Возможность подключения к исходному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                                       |
| Именованный экземпляр SQL Server | 1434                                                  | Протокол UDP          | Любой        | локальное адресное пространство; | Allow      | Возможность подключения к исходному именованному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                        |
| Общий доступ по SMB                 | 445                                                   | TCP          | Любой        | локальное адресное пространство; | Allow      | Общий сетевой доступ по SMB в DMS для хранения файлов резервных копий базы данных с целью переноса в управляемый экземпляр Базы данных SQL Azure и SQL Server на виртуальной машине Azure. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.) |
| DMS_subnet                | Любой                                                   | Любой          | Любой        | DMS_subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>См. также

- [Миграция SQL Server в SQL Управляемый экземпляр](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Общие сведения о предварительных требованиях для использования Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Создание виртуальной сети с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Дальнейшие шаги

- Общие сведения о Azure Database Migration Service см. в статье [что такое Azure Database Migration Service?](dms-overview.md).
- Текущие сведения о региональных доступности Azure Database Migration Service см. на странице доступность [продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .

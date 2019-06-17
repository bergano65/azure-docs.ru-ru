---
title: Сетевые топологии для переноса базы данных управляемого экземпляра SQL Azure с помощью Azure Database Migration Service | Документация Майкрософт
description: Узнайте, исходной и целевой конфигурациях для Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808423"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Сетевые топологии для переноса DB управляемый экземпляр SQL Azure с помощью Azure Database Migration Service

В этой статье рассматриваются различные топологии сети, которыми может работать Azure Database Migration Service для комплексное переноса из локальных систем SQL Server для базы данных управляемого экземпляра SQL Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Управляемый экземпляр Базы данных SQL Azure, настроенный для гибридных рабочих нагрузок 

Используйте эту топологию, если Управляемый экземпляр Базы данных SQL Azure подключен к локальной сети. Такой подход упрощает сетевую маршрутизацию и максимально повышает пропускную способность во время миграции.

![Сетевая топология для гибридных рабочих нагрузок](media/resource-network-topologies/hybrid-workloads.png)

**Требования**

- В этом случае Azure базы данных SQL управляемого экземпляра и экземпляр Azure Database Migration Service, создаются в одной и той же виртуальной сети Azure, но они используют разные подсети.  
- Виртуальная сеть, используемая в этом сценарии также подключена к локальной сети с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Управляемый экземпляр Базы данных SQL Azure, изолированный от локальной сети

Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:

- Управляемый экземпляр базы данных SQL Azure изолирован от локального подключения, но экземпляр Azure Database Migration Service подключен к локальной сети.
- Если политики управления на основе доступа ролей (RBAC) доступны в месте, и вам нужно ограничить пользователей с доступом к той же подписке, на котором размещается управляемый экземпляр базы данных SQL Azure.
- Виртуальные сети, используемых для базы данных управляемого экземпляра SQL Azure и Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для Управляемого экземпляра Базы данных SQL Azure, изолированного от локальной сети](media/resource-network-topologies/mi-isolated-workload.png)

**Требования**

- Виртуальная сеть, которую использует Azure Database Migration Service для этого сценария также должна быть подключена к локальной сети с помощью (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Настройка [пиринг](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) между виртуальная сеть, используемая для базы данных SQL управляемого экземпляра и Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Cloud-to-cloud миграция: общая виртуальная сеть

Используйте эту топологию, если источник SQL Server размещается на виртуальной Машине Azure и той же виртуальной сети, совместно управляемые базы данных SQL Azure и Azure Database Migration Service.

![Сетевая топология для миграции в облако с общей виртуальной сетью](media/resource-network-topologies/cloud-to-cloud.png)

**Требования**

- Дополнительных требований нет.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Облаками: изолированная виртуальная сеть

Используйте эту сетевую топологию, если в вашей среде нужно применить один или несколько из следующих сценариев:

- Управляемый экземпляр базы данных SQL Azure подготавливается в изолированной виртуальной сети.
- Если политики управления на основе доступа ролей (RBAC) доступны в месте, и вам нужно ограничить пользователей с доступом к той же подписке, на котором размещается управляемый экземпляр базы данных SQL Azure.
- Виртуальные сети, используемый для базы данных управляемого экземпляра SQL Azure и Azure Database Migration Service, находятся в разных подписках.

![Сетевая топология для миграции в облако с изолированной виртуальной сети](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Требования**

- Настройка [пиринг](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) между виртуальная сеть, используемая для базы данных SQL управляемого экземпляра и Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Правила безопасности для входящего трафика

| **ИМЯ**   | **ПОРТ** | **ПРОТОКОЛ** | **ИСТОЧНИК** | **НАЗНАЧЕНИЕ** | **ДЕЙСТВИЕ** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Любой      | Любой          | Подсеть DMS | Любой             | Allow      |

## <a name="outbound-security-rules"></a>Правила безопасности для исходящего трафика

| **ИМЯ**                  | **ПОРТ**                                              | **ПРОТОКОЛ** | **ИСТОЧНИК** | **НАЗНАЧЕНИЕ**           | **ДЕЙСТВИЕ** | **Причина для правила**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| управление                | 443,9354                                              | TCP          | Любой        | Любой                       | Allow      | Управляющее взаимодействие по служебной шине с Хранилищем BLOB-объектов Azure. <br/>(Если включен пиринг Майкрософт, это правило может не понадобиться.)                                                             |
| Диагностика               | 12000                                                 | TCP          | Любой        | Любой                       | Allow      | DMS использует это правило для сбора диагностических сведений для устранения неполадок.                                                                                                                      |
| Исходный SQL Server         | 1433 (или порт TCP IP, который прослушивает SQL Server) | TCP          | Любой        | локальное адресное пространство; | Allow      | Возможность подключения к исходному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                                       |
| Именованный экземпляр SQL Server | 1434                                                  | UDP          | Любой        | локальное адресное пространство; | Allow      | Возможность подключения к исходному именованному экземпляру SQL Server из DMS. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.)                                                                        |
| Общий доступ по SMB                 | 445                                                   | TCP          | Любой        | локальное адресное пространство; | Allow      | Общий сетевой доступ по SMB в DMS для хранения файлов резервных копий базы данных с целью переноса в управляемый экземпляр Базы данных SQL Azure и SQL Server на виртуальной машине Azure. <br/>(Если есть подключение типа "сайт-сайт", это правило может не понадобиться.) |
| DMS_subnet                | Любой                                                   | Любой          | Любой        | DMS_subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>См. также

- [Migrate SQL Server to Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) (Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure)
- [Предварительные требования для с помощью Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Создание виртуальной сети с помощью портала Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Дальнейшие действия

- Обзор Azure Database Migration Service, см. в статье [что такое Azure Database Migration Service?](dms-overview.md).
- Текущий сведения о региональной доступности службы Azure Database Migration Service, см. в разделе [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) страницы.

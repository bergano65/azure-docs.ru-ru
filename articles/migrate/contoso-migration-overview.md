---
title: Серия миграции Contoso Документы Майкрософт
description: Ссылки на примеры сценариев миграции Contoso для миграции в Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676332"
---
# <a name="contoso-migration-series"></a>Миграция Contoso: серия


У нас есть серия статей, демонстрирующие, как фиктивная организация Contoso переносит свою локта-инфраструктуру в облако [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Серия включает в себя сценарии, иллюстрирующие, как настроить миграцию инфраструктуры и как запускать различные типы миграций. Сценарии усложняют сядр по мере их развития. Статьи показывают, как компания Contoso обрабатывает миграцию, но общие инструкции и указатели предоставляются во всем.

## <a name="migration-articles"></a>Статьи о миграции

В таблице ниже приведено краткое описание статей из этого цикла.  

- Каждый сценарий миграции определяется несколько иными бизнес-требованиями, определяющими стратегию миграции.
- Для каждого сценария развертывания мы предоставляем информацию о бизнес-драйверах и целях, предлагаемую архитектуру, шаги для выполнения миграции и рекомендации по очистке и дальнейшим шагам после завершения миграции.


**Статьи** | **Сведения** 
--- | --- 
[Статья 1. Общие сведения](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Обзор серии статей, стратегии миграции компании Contoso и используемых в этой серии примеров приложений. 
[Статья 2. Развертывание инфраструктуры миграции в Contoso](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso готовит свою локта-инфраструктуру и инфраструктуру Azure для миграции. Для всех статей в серии используется одна и та же инфраструктура. 
[Статья 3. Оценка готовности локальных ресурсов к переносу в Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Компания Contoso выполняет оценку локального приложения SmartHotel360 в VMware. Для оценки виртуальных машин приложения Contoso использует службу "Миграция Azure". Для оценки базы данных SQL Server приложения используется Помощник по миграции данных.
[Статья 4. Повторное размещение приложения на виртуальной машине Azure и в Управляемом экземпляре Базы данных SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Специалисты компании Contoso переносят локальное приложение SmartHotel360 в Azure по методу lift-and-shift. Contoso мигрирует с передним VM-приложением с помощью [Azure Migrate.](https://docs.microsoft.com/azure/migrate/migrate-services-overview) Contoso переносит базу данных приложений в управляемую базу данных Azure S'L, используя [миграционную службу базы данных Azure.](https://docs.microsoft.com/azure/dms/dms-overview)
[Статья 5. Перемещение приложения в виртуальные машины Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso переносит свои виши в приложение SmartHotel360 на VMs-технологии Azure с помощью службы Azure Migrate. 
[Статья 6. Повторное размещение приложения на виртуальных машинах Azure и в группе доступности SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Компания Contoso переносит приложение SmartHotel360. Contoso использует Azure Migrate для переноса в приложение VMs. Она использует службу Azure Database Migration Service для миграции базы данных приложения в кластер SQL Server, который защищен группой доступности AlwaysOn. 
[Статья 7. Повторное размещение приложения Linux на виртуальных машинах Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso завершает миграцию и перемещение своего приложения Linux osTicket в Azure VMs с помощью Azure Migrate.
[Статья 8. Повторное размещение приложения Linux на виртуальных машинах Azure и в Базе данных Azure для MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso мигрирует своим приложением Linux osTicket на VMs-мв., использующих Azure Migrate. Он мигрирует базой данных приложения в базу данных Azure для MyS, используя службу миграции базы данных Azure (включает альтернативную опцию с использованием MyS'L Workbench).
[Статья 9. Рефакторинг приложения в веб-приложении Azure и Базе данных SQL Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso переносит свое приложение SmartHotel360 в веб-приложение Azure и переносит базу данных приложения в экземпляр Azure S'L Server с помощью службы миграции базы данных Azure.
[Статья 10: Рефакторинг приложения Windows с помощью служб приложений Azure и управляемых экземпляров S'L](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso мигрирует на основе windows-приложение в веб-приложение Azure и мигрирует базу данных приложения в управляемый экземпляр Azure S'L с помощью службы миграции базы данных Azure.
[Статья 11: Рефакторинг приложения Linux в веб-приложении Azure и базе данных Azure для MyS'L](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso переносит свое приложение Linux osTicket в веб-приложение Azure в нескольких регионах Azure, используя Azure Traffic Manager, интегрированный с GitHub для непрерывной доставки. Компания Contoso переносит базу данных приложения в экземпляр Базы данных Azure для MySQL. 
[Статья 12: Рефакторный сервер Team Foundation на сервисах Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Специалисты компании Contoso переносят локальное развертывание Team Foundation Server в Azure DevOps Services в Azure.
[Статья 13. Повторное создание приложения в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso восстанавливает свое приложение SmartHotel, используя ряд возможностей и служб Azure, включая службу приложений Azure, службу Azure Kubernetes (AKS), функции Azure, azure Cognitive Services и DB Azure Cosmos.
[Статья 14. Масштабирование миграции в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Ознакомившись с вариантами сочетаний миграции, компания Contoso готовится к полномасштабной миграции в Azure.



## <a name="next-steps"></a>Следующие шаги

- [Узнайте о](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) миграции облака.
- Узнайте о стратегиях миграции для других сценариев (источник/целевые пары) в [Руководстве по миграции баз данных.](https://datamigration.microsoft.com/)

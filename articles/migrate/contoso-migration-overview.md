---
title: Серия миграции Contoso Документы Майкрософт
description: Статья содержит общие сведения о стратегии и сценариях миграции, применяемых организацией Contoso для переноса своего локального центра обработки данных в Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78939191"
---
# <a name="contoso-migration-series"></a>Миграция Contoso: серия


У нас есть серия статей, демонстрирующие, как фиктивная организация Contoso мигрирует на локта-инфраструктуру в облако [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Этот цикл содержит сведения и сценарии, иллюстрирующие настройку инфраструктуры миграции и выполнение миграции различных типов. Сценарии усложняют сядр по мере их развития. В статьях описано, как компания Contoso выполняет собственную задачу по миграции, но в материалы также включены дополнительные материалы и конкретные инструкции.

## <a name="migration-articles"></a>Статьи о миграции

В таблице ниже приведено краткое описание статей из этого цикла.  

- Каждый сценарий миграции определяется немного отличающимися бизнес-целями, которые определяют стратегию миграции.
- Для каждого сценария развертывания мы предоставляем сведения о бизнес-факторах и целях, предлагаемой архитектуре, шагах по выполнению миграции, а также рекомендации по очистке и дальнейшим действиям после завершения миграции.

**Статьи** | **Подробно** 
--- | --- 
[Статья 1. Общие сведения](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Обзор серии статей, стратегии миграции компании Contoso и используемых в этой серии примеров приложений. 
[Статья 2. Развертывание инфраструктуры миграции в Contoso](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Компания Contoso готовит локальную инфраструктуру и инфраструктуру Azure для миграции. Для всех статей миграции в серии используется одна и та же инфраструктура. 
[Статья 3. Оценка готовности локальных ресурсов к переносу в Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Компания Contoso выполняет оценку локального приложения SmartHotel360 в VMware. Для оценки виртуальных машин приложения Contoso использует службу "Миграция Azure". Для оценки базы данных SQL Server приложения используется Помощник по миграции данных.
[Статья 4. Повторное размещение приложения на виртуальной машине Azure и в Управляемом экземпляре Базы данных SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Специалисты компании Contoso переносят локальное приложение SmartHotel360 в Azure по методу lift-and-shift. Специалисты компании Contoso переносят виртуальную машину внешнего интерфейса приложения с помощью [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Они переносят базу данных приложения в Управляемый экземпляр Базы данных SQL Azure с помощью [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Статья 5. Перемещение приложения в виртуальные машины Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Специалисты компании Contoso переносят виртуальные машины приложения SmartHotel360 на виртуальные машины Azure с помощью службы Site Recovery. 
[Статья 6. Повторное размещение приложения на виртуальных машинах Azure и в группе доступности SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Компания Contoso переносит приложение SmartHotel360. Для переноса виртуальных машин приложения компания Contoso использует Site Recovery. Она использует службу Azure Database Migration Service для миграции базы данных приложения в кластер SQL Server, который защищен группой доступности AlwaysOn. 
[Статья 7. Повторное размещение приложения Linux на виртуальных машинах Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Специалисты компании Contoso выполняют миграцию приложения osTicket для Linux по методу lift-and-shift на виртуальные машины Azure с помощью службы Site Recovery.
[Статья 8. Повторное размещение приложения Linux на виртуальных машинах Azure и в Базе данных Azure для MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Специалисты компании Contoso выполняют миграцию приложения osTicket для Linux на виртуальные машины Azure с помощью Site Recovery. Они перемещают базу данных приложения в Базу данных Azure для MySQL с помощью MySQL Workbench. 
[Статья 9. Рефакторинг приложения в веб-приложении Azure и Базе данных SQL Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Специалисты компании Contoso переносят приложение SmartHotel360 в веб-приложение Azure, а базу данных приложения — в экземпляр SQL Server Azure с помощью Помощника по миграции баз данных.     
[Статья 10. Рефакторинг приложения Linux в веб-приложении Azure и Базе данных SQL Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Специалисты компании Contoso переносят свое приложение osTicket для Linux в веб-приложение Azure в нескольких регионах Azure с помощью диспетчера трафика Azure, интегрированного с GitHub для непрерывной поставки. Компания Contoso переносит базу данных приложения в экземпляр Базы данных Azure для MySQL. 
[Статья 11. Рефакторинг развертывания Team Foundation Server в Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Специалисты компании Contoso переносят локальное развертывание Team Foundation Server в Azure DevOps Services в Azure.
[Статья 12. Перепроектирование приложения в контейнерах Azure и Базе данных SQL Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Специалисты компании Contoso переносят приложение SmartHotel в Azure. Затем уровень веб-приложений преобразуется в контейнер Windows, работающий в Azure Service Fabric, и базу данных в службе "База данных SQL Azure".
[Статья 13. Повторное создание приложения в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Специалисты компании Contoso выполняют повторную сборку приложения SmartHotel, используя ряд возможностей и служб Azure, включая Службу приложений Azure, Службу Azure Kubernetes (AKS), Функции Azure, Cognitive Services и Azure Cosmos DB.
[Статья 14. Масштабирование миграции в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Ознакомившись с вариантами сочетаний миграции, компания Contoso готовится к полномасштабной миграции в Azure.

## <a name="next-steps"></a>Дальнейшие действия

- [Узнайте о](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) миграции облака.
- Узнайте о стратегиях миграции для других сценариев (источник/целевые пары) в [Руководстве по миграции баз данных.](https://datamigration.microsoft.com/)

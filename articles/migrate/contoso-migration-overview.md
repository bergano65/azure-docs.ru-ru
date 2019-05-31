---
title: Серия миграции Contoso | Документация Майкрософт
description: Статья содержит общие сведения о стратегии и сценариях миграции, применяемых организацией Contoso для переноса своего локального центра обработки данных в Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: bf2a8dec919980db616cd927639cdd2f695720c5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238027"
---
# <a name="contoso-migration-series"></a>Миграция Contoso: серия


У нас есть ряд статьи, в которых показано, как вымышленная организация переносит Contoso в локальной инфраструктуре для [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) облака. 

Этот цикл содержит сведения и сценарии, иллюстрирующие настройку инфраструктуры миграции и выполнение миграции различных типов. Сценарии увеличению сложности, что по мере продвижения. В статьях описано, как компания Contoso выполняет собственную задачу по миграции, но в материалы также включены дополнительные материалы и конкретные инструкции.

## <a name="migration-articles"></a>Статьи о миграции

В таблице ниже приведено краткое описание статей из этого цикла.  

- Каждый сценарий миграции определяется немного отличающимися бизнес-целями, которые определяют стратегию миграции.
- Для каждого сценария развертывания мы предоставляем сведения о бизнес-факторах и целях, предлагаемой архитектуре, шагах по выполнению миграции, а также рекомендации по очистке и дальнейшим действиям после завершения миграции.

**Статья** | **Сведения** 
--- | --- 
[Статья 1. Общие сведения](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Обзор серии статей, стратегии миграции компании Contoso и используемых в этой серии примеров приложений. 
[Статья 2. Развертывание инфраструктуры Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Компания Contoso готовит локальную инфраструктуру и инфраструктуру Azure для миграции. Для всех статей миграции в серии используется одна и та же инфраструктура. 
[Статья 3. Оценка локальных ресурсов для миграции в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Компания Contoso выполняет оценку локального приложения SmartHotel360 в VMware. Для оценки виртуальных машин приложения Contoso использует службу "Миграция Azure". Для оценки базы данных SQL Server приложения используется Помощник по миграции данных.
[Статья 4. Повторное размещение приложения на виртуальной машине Azure и в Управляемом экземпляре Базы данных SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Специалисты компании Contoso переносят локальное приложение SmartHotel360 в Azure по методу lift-and-shift. Специалисты компании Contoso переносят виртуальную машину внешнего интерфейса приложения с помощью [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Они переносят базу данных приложения в Управляемый экземпляр Базы данных SQL Azure с помощью [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Статья 5. Миграция приложения компании Contoso: повторное размещение локального приложения на виртуальных машинах Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Специалисты компании Contoso переносят виртуальные машины приложения SmartHotel360 на виртуальные машины Azure с помощью службы Site Recovery. 
[Статья 6. Повторное размещение приложения на виртуальных машинах Azure и в группе доступности SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Компания Contoso переносит приложение SmartHotel360. Для переноса виртуальных машин приложения компания Contoso использует Site Recovery. Она использует службу Azure Database Migration Service для миграции базы данных приложения в кластер SQL Server, который защищен группой доступности AlwaysOn. 
[Статья 7. Перенос приложения компании Contoso: повторное размещение локального приложения Linux на виртуальных машинах Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Специалисты компании Contoso выполняют миграцию приложения osTicket для Linux по методу lift-and-shift на виртуальные машины Azure с помощью службы Site Recovery.
[Статья 8. Миграция в компании Contoso. Повторное размещение локального приложения Linux в виртуальных машинах Azure и MySQL в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Специалисты компании Contoso выполняют миграцию приложения osTicket для Linux на виртуальные машины Azure с помощью Site Recovery. Они перемещают базу данных приложения в Базу данных Azure для MySQL с помощью MySQL Workbench. 
[Статья 9. Миграция в компании Contoso: рефакторинг локального приложения для веб-приложения Azure и Базы данных SQL Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Специалисты компании Contoso переносят приложение SmartHotel360 в веб-приложение Azure, а базу данных приложения — в экземпляр SQL Server Azure с помощью Помощника по миграции баз данных.     
[Статья 10. Миграция в компании Contoso: рефакторинг приложения службы поддержки Linux для использования нескольких регионов со Службой приложений Azure, диспетчером трафика и MySQL Azure в Contoso](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Специалисты компании Contoso переносят свое приложение osTicket для Linux в веб-приложение Azure в нескольких регионах Azure с помощью диспетчера трафика Azure, интегрированного с GitHub для непрерывной поставки. Компания Contoso переносит базу данных приложения в экземпляр Базы данных Azure для MySQL. 
[Статья 11. Миграция Contoso: рефакторинг развертывания Team Foundation Server в Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Специалисты компании Contoso переносят локальное развертывание Team Foundation Server в Azure DevOps Services в Azure.
[Статья 12. Миграция в компании Contoso: повторное проектирование локального приложения для использования контейнера Azure и Базы данных SQL Azure](c https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Специалисты компании Contoso переносят приложение SmartHotel в Azure. Затем уровень веб-приложений преобразуется в контейнер Windows, работающий в Azure Service Fabric, и базу данных в службе "База данных SQL Azure". 
[Статья 13. Повторное создание приложения в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Специалисты компании Contoso выполняют повторную сборку приложения SmartHotel, используя ряд возможностей и служб Azure, включая Службу приложений Azure, Службу Azure Kubernetes (AKS), Функции Azure, Cognitive Services и Azure Cosmos DB.  
[Статья 14. Масштабирование миграции в Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Ознакомившись с вариантами сочетаний миграции, компания Contoso готовится к полномасштабной миграции в Azure. 


    

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) Миграция в облако. 


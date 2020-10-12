---
title: Серия миграции contoso | Документация Майкрософт
description: Ссылки на примеры сценариев миграции Contoso для миграции в Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107637"
---
# <a name="contoso-migration-series"></a>Миграция Contoso: серия


У нас есть серия статей, демонстрирующих, как вымышленная организация Contoso переносит свою локальную инфраструктуру в облако [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

В серию входят сценарии, иллюстрирующие настройку миграции инфраструктуры и выполнение различных типов миграции. Сценарии увеличивают сложность по мере их продвижения. В статьях показано, как компания Contoso обрабатывает миграцию, но общие инструкции и указатели предоставляются по всему предустановке.

## <a name="migration-articles"></a>Статьи о миграции

В таблице ниже приведено краткое описание статей из этого цикла.  

- Каждый сценарий миграции управляется различными бизнес-требованиями, которые определяют стратегию миграции.
- Для каждого сценария развертывания мы предоставляем сведения о бизнес-драйверах и целях, предлагаемую архитектуру, шаги для выполнения миграции и рекомендации по очистке и дальнейшим действиям после завершения миграции.


**Статья** | **Сведения** 
--- | --- 
[Статья 1. Общие сведения](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Обзор серии статей, стратегии миграции компании Contoso и используемых в этой серии примеров приложений. 
[Статья 2. Развертывание инфраструктуры миграции в Contoso](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Компания Contoso готовит свою локальную инфраструктуру и инфраструктуру Azure для миграции. Для всех статей в серии используется одна и та же инфраструктура. 
[Статья 3. Оценка готовности локальных ресурсов к переносу в Azure](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Компания Contoso выполняет оценку локального приложения SmartHotel360 в VMware. Для оценки виртуальных машин приложения Contoso использует службу "Миграция Azure". Для оценки базы данных SQL Server приложения используется Помощник по миграции данных.
[Статья 4. размещение приложения на виртуальной машине Azure и Управляемый экземпляр SQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Специалисты компании Contoso переносят локальное приложение SmartHotel360 в Azure по методу lift-and-shift. Contoso переносит клиентскую виртуальную машину приложения с помощью службы " [Миграция Azure](./migrate-services-overview.md)". Компания Contoso переносит базу данных приложений на Управляемый экземпляр SQL с помощью [Azure Database Migration Service](../dms/dms-overview.md).
[Статья 5. Перемещение приложения в виртуальные машины Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Компания Contoso переносит свои виртуальные машины приложений SmartHotel360 на виртуальные машины Azure с помощью службы "миграция Azure". 
[Статья 6. Повторное размещение приложения на виртуальных машинах Azure и в группе доступности SQL Server AlwaysOn](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Компания Contoso переносит приложение SmartHotel360. Contoso использует миграцию Azure для переноса виртуальных машин приложений. Она использует службу Azure Database Migration Service для миграции базы данных приложения в кластер SQL Server, который защищен группой доступности AlwaysOn. 
[Статья 7. Повторное размещение приложения Linux на виртуальных машинах Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Компания Contoso завершает миграцию своего приложения Остиккет на виртуальные машины Azure с помощью миграции с переносом на новую машину, используя службу "миграция Azure".
[Статья 8. Повторное размещение приложения Linux на виртуальных машинах Azure и в Базе данных Azure для MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Компания Contoso переносит свое приложение Остиккет Linux на виртуальные машины Azure с помощью службы "миграция Azure". Она переносит базу данных приложения в базу данных Azure для МИСК, используя Azure Database Migration Service (в том числе альтернативный вариант использования MySQL Workbench).
[Статья 9. Рефакторинг приложения в веб-приложении Azure и Базе данных SQL Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Компания Contoso переносит свое приложение SmartHotel360 в веб-приложение Azure и переносит базу данных приложения в базу данных SQL Azure с помощью Azure Database Migration Service.
[Статья 10. рефакторинг приложения Windows с помощью служб приложений Azure и SQL Управляемый экземпляр](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso переносит локальное приложение на основе Windows в веб-приложение Azure и переносит базу данных приложения в Управляемый экземпляр Azure SQL с помощью Azure Database Migration Service.
[Статья 11. рефакторинг приложения Linux в веб-приложении Azure и базе данных Azure для MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Компания Contoso переносит свое приложение Остиккет для Linux в веб-приложение Azure в нескольких регионах Azure с помощью диспетчера трафика Azure, интегрированного с GitHub для непрерывной доставки. Компания Contoso переносит базу данных приложения в экземпляр Базы данных Azure для MySQL. 
[Статья 12. рефакторинг Team Foundation Server в Azure DevOps Services](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Специалисты компании Contoso переносят локальное развертывание Team Foundation Server в Azure DevOps Services в Azure.
[Статья 13. Повторное создание приложения в Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Компания Contoso перестраивает свое приложение Смарсотел, используя ряд возможностей и служб Azure, включая службу приложений Azure, службу Kubernetes Azure (AKS), функции Azure, Cognitive Services Azure и Azure Cosmos DB.
[Статья 14. Масштабирование миграции в Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Ознакомившись с вариантами сочетаний миграции, компания Contoso готовится к полномасштабной миграции в Azure.



## <a name="next-steps"></a>Дальнейшие действия

- [Сведения о](/azure/architecture/cloud-adoption/migrate/) миграции в облако.
- Дополнительные сведения о стратегиях миграции для других сценариев (пары «источник-назначение») см. в статье о [миграции базы данных](https://datamigration.microsoft.com/).

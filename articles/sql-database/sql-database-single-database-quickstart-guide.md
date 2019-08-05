---
title: Краткое руководство. Использование отдельных баз данных в Базе данных SQL Azure | Документация Майкрософт
description: Узнайте, как быстро приступить к работе с отдельной базой данных в Базе данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 45b0b2bc7a2ef5c3cb6ca801668f7b5be7c8ac73
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639991"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Начало работы с отдельной базой данных в Базе данных SQL Azure

[Отдельная база данных](sql-database-single-index.yml) — это полностью управляемая база данных как услуга (DbaaS) на основе модели PaaS. Это идеальная подсистема хранилища для современных облачных приложений. В этом разделе вы узнаете, как быстро настроить и создать отдельную базу данных в Базе данных SQL.

## <a name="quickstart-overview"></a>Общие сведения о кратком руководстве

В этом разделе представлен обзор доступных статей, которые помогут вам быстро начать работу с отдельными базами данных. В приведенных ниже кратких руководствах показано, как быстро создать отдельную базу данных, настроить правило брандмауэра для сервера базы данных, а затем импортировать базу данных в новую отдельную базу данных с использованием файла `.bacpac`:

- [Создайте отдельную базу данных с помощью портала Azure](sql-database-single-database-get-started.md).
- После создания базы данных [ее необходимо защитить, настроив правила брандмауэра](sql-database-server-level-firewall-rule.md).
- Если на сервере SQL Server есть база данных, которую вы хотите перенести в Azure, необходимо установить [Помощник по миграции данных (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Он проанализирует ваши базы данных на SQL Server и обнаружит любые проблемы, которые могут заблокировать возможность переноса в развертывание в виде отдельной базы данных. Если вы не обнаружите никаких проблем, вы можете экспортировать базу данных в виде файла `.bacpac` и [импортировать его с помощью портала Azure или SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Автоматизация операций управления

Для создания, настройки и масштабирования базы данных можно использовать PowerShell или Azure CLI.

- [Создание и настройка отдельной базы данных с помощью PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Создание и настройка отдельной базы данных с помощью Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [Обновление отдельной базы данных и масштабирование ресурсов с помощью PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Обновление отдельной базы данных и масштабирование ресурсов с помощью Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Перенос в отдельную базу данных с минимальным временем простоя

Эти краткие руководства позволяют быстро создать или импортировать базу данных в Azure с использованием файла `.bacpac`. Однако файлы `.bacpac` и `.dacpac` предназначены для быстрого перемещения баз данных между различными версиями SQL Server и вариантами развертывания в Базе данных SQL Azure или для осуществления непрерывной интеграции в конвейер DevOps. Этот метод не обеспечивает перенос рабочих баз данных с минимальным временем простоя, так как вам потребуется остановить добавление новых данных, дождаться завершения экспортирования базы данных-источника в файл `.bacpac`, а затем — завершения его импорта в базу данных SQL Azure. Все это ожидание приведет к простою приложения, особенно для больших баз данных. Чтобы переместить свою рабочую базу данных, вам понадобится лучший способ переноса, который обеспечивает минимальный простой. Для переноса базы данных с минимальными простоями используйте [службу миграции данных (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json), которая выполняет постепенную принудительную отправку изменений, внесенных в базу данных-источник, в отдельную восстанавливаемую базу данных. С ее помощью вы можете быстро перевести приложение с исходной базы данных на целевую с минимальным временем простоя.

## <a name="hands-on-learning-modules"></a>Практические учебные модули

С помощью приведенных ниже бесплатных модулей Microsoft Learn вы сможете получить сведения о службе "База данных SQL Azure".

- [Подготовка базы данных SQL Azure для хранения данных приложения](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Разработка и настройка приложения ASP.NET, обращающегося к Базе данных SQL Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Защита базы данных SQL Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [кратким списком поддерживаемых возможностей в Базе данных SQL Azure](sql-database-features.md).
- Узнайте, как [повысить безопасность базы данных](sql-database-security-tutorial.md).
- Дополнительные руководства см. в статье [Использование отдельной базы данных в Базе данных SQL Azure](sql-database-howto-single-database.md).
- Ознакомьтесь с примерами скриптов, написанными в [PowerShell](sql-database-powershell-samples.md) и [Azure CLI](sql-database-cli-samples.md).
- Узнайте больше об [API управления](sql-database-single-databases-manage.md), который можно использовать для настройки баз данных.
- [Identify the right Azure SQL Database/Managed Instance SKU for your on-premises database](/sql/dma/dma-sku-recommend-sql-db/) (Определение нужного SKU Базы данных SQL Azure и Управляемого экземпляра для локальной базы данных).

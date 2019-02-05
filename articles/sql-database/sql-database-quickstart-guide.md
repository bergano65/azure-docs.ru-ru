---
title: Краткое руководство. База данных SQL Azure | Документация Майкрософт
description: Узнайте, как быстро приступить к работе с отдельной Базой данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: dad9f004b49ee98e6efdd7d10b779ec3bd81fa47
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55105715"
---
# <a name="getting-started-with-azure-sql-database-singleton"></a>Начало работы с отдельной Базой данных SQL Azure

[Отдельная База данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) (или отдельная база данных) — это полностью управляемая база данных как услуга (DbaaS) на основе модели PaaS, которая является идеальной подсистемой хранилища для современных облачных приложений. В этом разделе вы узнаете, как быстро настроить и создать Базу данных SQL.

## <a name="quickstart-overview"></a>Общие сведения о кратком руководстве

В этом разделе представлен обзор доступных статей, которые помогут вам быстро начать работу с отдельной базой данных. Самый простой способ создать первую Базу данных SQL — использовать [портал Azure](sql-database-get-started-portal.md), где можно настроить необходимые параметры.
После создания [базу данных необходимо защитить, настроив правила брандмауэра](sql-database-get-started-portal-firewall.md). 

Если на сервере SQL Server есть база данных, которую вы хотите перенести в Azure, необходимо установить [Помощник по миграции данных (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Он проанализирует ваши базы данных на SQL Server и обнаружит любые проблемы, которые могут заблокировать перенос в отдельную базу данных. Если вы не обнаружите никаких проблем, вы можете экспортировать базу данных в виде файла `.bacpac` и [импортировать его с помощью портала Azure или SqlPackage](sql-database-import.md).

Эти краткие руководства позволяют быстро настраивать, создавать и импортировать базы данных в облако Azure.

## <a name="automating-management-operations"></a>Автоматизация операций управления

Портал Azure позволяет с легкостью создать и изменить отдельную базу данных Azure. Для создания баз данных также можно использовать [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) или [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).
Кроме того, с помощью [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) или [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) можно обновить отдельную базу данных и масштабировать ресурсы.

## <a name="migrating-to-azure-single-database-with-minimal-downtime"></a>Перенос в отдельную базу данных Azure с минимальным временем простоя

Статьи в этих кратких руководствах позволяют быстро создать или импортировать базу данных в Azure с помощью `.bacpac`. Однако файлы `.bacpac` и `.dacpack` предназначены для быстрого перемещения базы данных между различными версиями SQL Server и Базы данных SQL Azure (отдельная база данных или Управляемый экземпляр) или для осуществления непрерывной интеграции в конвейер DevOps. Тем не менее этот метод не предназначен для перемещения рабочих баз данных с минимальным временем простоя, так как необходимо будет подождать, чтобы экспортировать базу данных-источник в виде файла `.bacpac` и импортировать ее в Базу данных SQL Azure, что приведет к некоторому простою приложения, особенно если база данных большая. Если вы перемещаете свою рабочую базу данных, вам, вероятно, понадобится лучший способ переноса, который обеспечивает минимальный простой. [Служба миграции данных](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) — это служба, которая может переносить базу данных с минимальным временем простоя. С ее помощью вы можете быстро перевести приложение с исходной базы данных на целевую с минимальным временем простоя.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с [кратким списком поддерживаемых возможностей в Базе данных SQL Azure](sql-database-features.md). 
* Узнайте, как [повысить безопасность базы данных](sql-database-security-tutorial.md). 
* Ознакомьтесь с дополнительными руководствами в [этом разделе](sql-database-howto-single-database.md). 
* Ознакомьтесь с примерами скриптов, написанными в [PowerShell](sql-database-powershell-samples.md) и [Azure CLI](sql-database-cli-samples.md). 
* Узнайте больше об [API управления](sql-database-single-databases-manage.md), который можно использовать для настройки баз данных. 

---
title: Примеры скриптов Azure PowerShell для базы данных SQL | Документация Майкрософт
description: Примеры скриптов Azure PowerShell помогут вам создать серверы базы данных SQL, эластичные пулы, базы данных и брандмауэры, а также помогут управлять ими.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/29/2018
ms.openlocfilehash: c53980dc2d29fbfb7e59dcba7252e0b0448d14d3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840016"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Примеры Azure PowerShell для базы данных SQL Azure

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для базы данных SQL Azure.

| |  |
|---|---|
|**Создание и настройка управляемого экземпляра, отдельной базы данных и эластичного пула**||
| [Создание отдельной базы данных и настройка правила брандмауэра](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает отдельную базу данных SQL Azure и настраивает правило брандмауэра на уровне сервера. |
| [Создание эластичных пулов и перемещение баз данных в составе пулов](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell создает эластичные пулы базы данных SQL Azure и перемещает базы данных в составе пулов, а также изменяет их вычислительную мощность.|
| [Создание управляемого экземпляра и управление им](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Этот скрипт PowerShell демонстрирует создание управляемого экземпляра и управление им с помощью Azure PowerShell. |
| [Создание Управляемого экземпляра Базы данных SQL с помощью шаблона Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell демонстрирует создание Управляемого экземпляра и управление им с помощью Azure PowerShell и шаблона Azure Resource Manager.|
|**Настройка георепликации и отработка отказа**||
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для отдельной базы данных SQL Azure и выполняет для нее отработку отказа во вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в составе пула с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для базы данных SQL Azure в эластичном пуле SQL и выполняет для нее отработку отказа во вторичную реплику. |
| [Настройка и отработка отказа для группы отработки отказа для отдельной базы данных](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell настраивает группу отработки отказа для экземпляра сервера базы данных SQL Azure, добавляет базу данных в группу отработки отказа и выполняет для нее отработку отказа на сервер-получатель. |
|**Масштабирование отдельных баз данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности базы данных SQL Azure, увеличивает ее вычислительную мощность и создает правило генерации оповещений для одной из метрик производительности. |
| [Масштабирование эластичного пула](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности эластичного пула базы данных SQL Azure, увеличивает его вычислительную мощность и создает правило генерации оповещений для одной из метрик производительности.  |
| **Аудит и обнаружение угроз** |
| [Настройка аудита и обнаружения угроз](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает политики аудита и обнаружения угроз для базы данных SQL Azure. |
| **Восстановление, копирование и импорт базы данных**||
| [Восстановление базы данных](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell восстанавливает базу данных SQL Azure из геоизбыточной резервной копии и восстанавливает последнюю резервную копию удаленной базы данных SQL Azure. |
| [Копирование базы данных на новый сервер](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell создает копию существующей базы данных SQL Azure на новом сервере SQL Azure. |
| [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell импортирует базу данных из BACPAC-файла на сервер SQL Azure. |
| **Синхронизация данных между базами данных**||
| [Синхронизация данных между базами данных SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает синхронизацию данных между несколькими базами данных SQL Azure. |
| [Синхронизация данных между базой данных SQL и локальной базой данных SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает синхронизацию данных между базой данных SQL Azure и локальной базой данных SQL Server. |
| [Изменение схемы синхронизации данных SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell добавляет или удаляет элементы в схеме синхронизации данных. |
|||

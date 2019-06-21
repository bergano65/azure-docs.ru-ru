---
title: Примеры скриптов Azure PowerShell для базы данных SQL | Документация Майкрософт
description: Примеры скриптов Azure PowerShell помогут вам создать серверы Базы данных SQL Azure, эластичные пулы, базы данных и брандмауэры, а также помогут управлять ими.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 03bec71cca445d580605c1b0f4705e619933000a
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729136"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Примеры Azure PowerShell для Базы данных SQL Azure

Служба "База данных SQL Azure" позволяет настраивать базы данных, экземпляры и пулы с помощью Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится AZ PowerShell 1.4.0 или более поздней версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="single-database-and-elastic-pools"></a>Отдельная база данных и эластичные пулы

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для Базы данных SQL Azure.

| |  |
|---|---|
|**Создание и настройка отдельных баз данных и эластичных пулов**||
| [Создание отдельной базы данных и настройка правила брандмауэра для сервера базы данных](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает отдельную базу данных SQL Azure и настраивает правило брандмауэра на уровне сервера. |
| [Создание эластичных пулов и перемещение баз данных в пуле](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell создает эластичные пулы базы данных SQL Azure и перемещает базы данных в пуле, а также изменяет их вычислительную мощность.|
|**Настройка георепликации и отработка отказа**||
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для отдельной базы данных SQL Azure и выполняет для нее отработку отказа во вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в пуле с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell настраивает активную георепликацию для базы данных SQL Azure в эластичном пуле SQL и выполняет для нее отработку отказа во вторичную реплику. |
| [Настройка и отработка отказа для группы отработки отказа для отдельной базы данных](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell настраивает группу отработки отказа для экземпляра сервера Базы данных SQL Azure, добавляет базу данных в группу отработки отказа и выполняет для нее отработку отказа на сервер-получатель. |
|**Масштабирование отдельной базы данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности базы данных SQL Azure, увеличивает объем ее вычислительных ресурсов и создает правило генерации оповещений для одной из метрик производительности. |
| [Масштабирование эластичного пула](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности эластичного пула базы данных SQL Azure, увеличивает объем его вычислительных ресурсов и создает правило генерации оповещений для одной из метрик производительности.  |
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

См. дополнительные сведения об [API управления отдельной базой данных с помощью Azure PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Управляемый экземпляр

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для Управляемого экземпляра Базы данных SQL Azure.

| |  |
|---|---|
|**Создание и настройка управляемых экземпляров**||
| [Создание управляемого экземпляра и управление им](scripts/sql-database-create-configure-managed-instance-powershell.md) | Этот скрипт PowerShell демонстрирует создание управляемого экземпляра и управление им с помощью Azure PowerShell. |
| [Создание Управляемого экземпляра Базы данных SQL с помощью шаблона Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell демонстрирует создание Управляемого экземпляра и управление им с помощью Azure PowerShell и шаблона Azure Resource Manager.|
| **Настройка прозрачного шифрования данных (TDE)**||
| [Управление прозрачным шифрованием данных в Управляемом экземпляре с использованием собственного ключа из Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell позволяет настроить прозрачное шифрование данных (TDE) в сценарии создания собственных ключей для Управляемого экземпляра SQL Azure с помощью ключа из Azure Key Vault.|
|||

См. дополнительные сведения об [API управления Управляемыми экземплярами с помощью Azure PowerShell](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

## <a name="additional-resources"></a>Дополнительные ресурсы

В примерах, приведенных на этой странице, используются [командлеты Базы данных SQL Azure](/powershell/module/az.sql/) для создания ресурсов SQL в Azure и управления ими. Дополнительные командлеты для выполнения запросов и множества других задач баз данных доступны в модуле [sqlserver](/powershell/module/sqlserver/). Подробные сведения см. в статье [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).

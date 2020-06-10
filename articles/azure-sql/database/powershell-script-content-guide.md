---
title: Примеры скриптов Azure PowerShell.
description: Примеры сценариев Azure PowerShell для создания ресурсов Базы данных SQL Azure и Управляемого экземпляра SQL Azure и управления ими
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 93fac2e3ae8d83f0fa49f94a3ba7880a2ecc2b4c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193901"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Примеры сценариев Azure PowerShell для Базы данных SQL Azure и Управляемого экземпляра SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Службы "База данных SQL Azure" и "Управляемый экземпляр SQL Azure" позволяют настраивать базы данных, экземпляры и пулы с помощью Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, вам понадобится AZ PowerShell 1.4.0 или последующей версии для работы с этим руководством. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="azure-sql-database"></a>[База данных SQL Azure](#tab/single-database)

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для Базы данных SQL Azure.

| |  |
|---|---|
|**Создание и настройка отдельных баз данных и эластичных пулов**||
| [Создание отдельной базы данных и настройка правила брандмауэра на уровне сервера](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell создает отдельную базу данных и настраивает правило брандмауэра для IP-адресов на уровне сервера. |
| [Создание эластичных пулов и перемещение баз данных в пуле](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell создает эластичные пулы и перемещает базы данных в составе пулов, а также изменяет их вычислительную мощность.|
|**Настройка георепликации и отработка отказа**||
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell настраивает активную георепликацию для отдельной базы данных и выполняет для нее отработку отказа на вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в пуле с помощью PowerShell](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell настраивает активную георепликацию для базы данных в эластичном пуле и выполняет для нее отработку отказа на вторичную реплику. |
|**Настройка группы отработки отказа**||
| [Настройка группы отработки отказа для отдельной базы данных](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает базу данных и группу отработки отказа, добавляет базу данных в эту группу и тестирует отработку отказа на сервер-получатель. |
| [Use PowerShell to add an Azure SQL Database elastic pool to a failover group](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Добавление эластичного пула Базы данных SQL Azure в группу отработки отказа с помощью PowerShell) | Этот скрипт PowerShell создает базу данных, добавляет ее в эластичный пул, добавляет эластичный пул в группу отработки отказа и тестирует отработку отказа на сервер-получатель. |
|**Масштабирование отдельной базы данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности отдельной базы данных, увеличивает объем ее вычислительных ресурсов и создает правило генерации оповещений для одной из метрик производительности. |
| [Масштабирование эластичного пула](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell отслеживает метрики производительности эластичного пула, увеличивает объем его вычислительных ресурсов и создает правило генерации оповещений для одной из метрик производительности. |
| **Аудит и обнаружение угроз** |
| [Настройка аудита и обнаружения угроз](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell настраивает политики аудита и обнаружения угроз для базы данных. |
| **Восстановление, копирование и импорт базы данных**||
| [Восстановление базы данных](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell восстанавливает базу данных из геоизбыточной резервной копии и восстанавливает последнюю резервную копию удаленной базы данных. |
| [Копирование базы данных на новый сервер](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell создает копию существующей базы данных на новом сервере. |
| [Импорт базы данных из BACPAC-файла](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell импортирует базу данных в базу данных SQL Azure из BACPAC-файла. |
| **Синхронизация данных между базами данных**||
| [Синхронизация данных между базами данных SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает технологию "Синхронизация данных" для синхронизации между несколькими базами данных в Базе данных SQL Azure. |
| [Синхронизация данных между Базой данных SQL и SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell настраивает технологию "Синхронизация данных" для синхронизации между базой данных в Базе данных SQL Azure и базой данных SQL Server. |
| [Изменение схемы синхронизации данных SQL](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell добавляет или удаляет элементы в схеме синхронизации данных. |
|||

См. дополнительные сведения об [API управления отдельной базой данных с помощью Azure PowerShell](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Управляемый экземпляр SQL Azure](#tab/managed-instance)

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для Управляемого экземпляра SQL Azure.

| |  |
|---|---|
|**Создание и настройка управляемых экземпляров**||
| [Создание управляемого экземпляра и управление им](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Этот сценарий PowerShell демонстрирует создание управляемого экземпляра и управление им с помощью Azure PowerShell. |
| [Создание управляемого экземпляра и управление им с помощью шаблона Azure Resource Manager](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот сценарий PowerShell демонстрирует создание управляемого экземпляра и управление им с помощью Azure PowerShell и шаблона Azure Resource Manager.|
| [Восстановление базы данных в управляемый экземпляр в другом географическом регионе](../managed-instance/scripts/restore-geo-backup.md) | Этот скрипт PowerShell восстанавливает резервную копию базы данных в другом регионе. Такой сценарий называется аварийным геовосстановлением. |
| **Настройка прозрачного шифрования данных (TDE)**||
| [Управление прозрачным шифрованием данных в управляемом экземпляре с использованием собственного ключа из Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell позволяет настроить прозрачное шифрование данных (TDE) в сценарии создания собственных ключей для Управляемого экземпляра SQL Azure с помощью ключа из Azure Key Vault.|
|**Настройка группы отработки отказа**||
| [Настройка группы отработки отказа для управляемого экземпляра](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Этот скрипт PowerShell создает два управляемых экземпляра, добавляет их в группу отработки отказа, а затем проверяет отработку отказа с основного на дополнительный управляемый экземпляр. |
|||

Дополнительные сведения о [командлетах PowerShell для Управляемого экземпляра Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Дополнительные ресурсы

В примерах, приведенных на этой странице, используются [командлеты PowerShell](/powershell/module/az.sql/) для создания ресурсов SQL Azure и управления ими. Дополнительные командлеты для выполнения запросов и множества других задач баз данных доступны в модуле [sqlserver](/powershell/module/sqlserver/). Подробные сведения см. в статье [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).

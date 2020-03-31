---
title: Примеры скриптов для Azure CLI
description: Примеры сценариев Azure CLI для создания серверов, эластичных пулов, баз данных, а также брандмауэров Базы данных SQL Azure и управления ими.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061713"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Примеры Azure CLI для Базы данных SQL Azure

Базу данных SQL Azure можно настроить с помощью <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Отдельная база данных и эластичные пулы](#tab/single-database)

| | |
|---|---|
|**Создание отдельной базы данных и эластичного пула**||
| [Создание отдельной базы данных и настройка правила брандмауэра](scripts/sql-database-create-and-configure-database-cli.md) | Создает базу данных SQL Azure и настраивает правило брандмауэра уровня сервера. |
| [Создание эластичных пулов и перемещение баз данных в пуле](scripts/sql-database-move-database-between-pools-cli.md) | Создает эластичные пулы SQL и перемещает базы данных SQL Azure в составе пулов, а также изменяет объемы вычислительных ресурсов. |
|**Масштабирование отдельной базы данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-cli.md) | Масштабирует базу данных SQL Azure до другого объема вычислительных ресурсов после запроса на получение сведений о размере базы данных. |
| [Масштабирование эластичного пула](scripts/sql-database-scale-pool-cli.md) | Масштабирует эластичный пул SQL, изменяя объем его вычислительных ресурсов. |
|**Настройка георепликации и отработка отказа**||
| [Добавление отдельных баз данных в группу отработки отказа](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Создает базу данных и группу отработки отказа, добавляет базу данных в эту группу, а потом тестирует отработку отказа на сервер-получатель. |
| [Use PowerShell to add an Azure SQL Database elastic pool to a failover group](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) (Добавление эластичного пула Базы данных SQL Azure в группу отработки отказа с помощью PowerShell) | Создает базу данных, добавляет ее в эластичный пул, добавляет эластичный пул в группу отработки отказа, а потом тестирует отработку отказа на сервер-получатель. |
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Настраивает активную георепликацию для базы данных SQL Azure и выполняет для нее отработку отказа на вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в пуле с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Настраивает активную георепликацию для базы данных SQL Azure в составе эластичного пула SQ, а потом выполняет для нее отработку отказа на вторичную реплику. |
| **Аудит и обнаружение угроз** |
| [Настройка аудита и обнаружения угроз](scripts/sql-database-auditing-and-threat-detection-cli.md)| Настраивает политики аудита и обнаружения угроз для базы данных SQL Azure. |
| **Резервное копирование, восстановление, копирование и импорт базы данных**||
| [Резервное копирование базы данных](scripts/sql-database-backup-database-cli.md)| Создает резервную копию базы данных SQL Azure в резервной копии хранилища Azure. |
| [Восстановление базы данных](scripts/sql-database-restore-database-cli.md)| Восстанавливает базу данных SQL Azure из геоизбыточной резервной копии и восстанавливает последнюю резервную копию удаленной базы данных SQL Azure. |
| [Копирование базы данных на новый сервер](scripts/sql-database-copy-database-to-new-server-cli.md) | Создает копию существующей базы данных SQL Azure на новом сервере SQL Azure. |
| [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-cli.md)| Импортирует базу данных из *.bacpac* на сервер SQL Azure. |
|||

Дополнительные сведения см. в разделе [Управление серверами баз данных и отдельными базами данных с помощью Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Управляемый экземпляр](#tab/managed-instance)

В следующей таблице содержатся ссылки на примеры скриптов Azure CLI для Управляемого экземпляра Базы данных SQL Azure.

| | |
|---|---|
| **Создание Управляемого экземпляра**||
| [Создание управляемого экземпляра](scripts/sql-database-create-configure-managed-instance-cli.md)| Создает управляемый экземпляр |
| **Настройка прозрачного шифрования данных (TDE)**||
| [Управление прозрачным шифрованием данных в Управляемом экземпляре с использованием Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Настраивает прозрачное шифрование данных (TDE) в Управляемом экземпляре Azure SQL используя Azure Key Vault с различными ключевыми сценариями. |
|**Настройка группы отработки отказа**||
| [Настройка группы отработки отказа для управляемого экземпляра](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Создает два управляемых экземпляра, добавляет их в группу отработки отказа, а затем проверяет отработку отказа с основного на дополнительный управляемый экземпляр. |
|||

Дополнительные примеры работы с Управляемым экземпляром см. в скриптах [создание](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [обновление](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [перемещение базы данных](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), [работа с](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Дополнительные сведения см. в разделе [Создание управляемых экземпляров с помощью Azure CLI, а также управление ими](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---

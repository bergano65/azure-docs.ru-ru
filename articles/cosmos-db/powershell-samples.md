---
title: Примеры Azure PowerShell для Azure Cosmos DB
description: Примеры Azure PowerShell — это скрипты для создания учетных записей базы данных Azure Cosmos DB и управления ими.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069300"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Примеры Azure PowerShell для базы данных Azure Cosmos DB

В приведенной ниже таблице содержатся ссылки на примеры сценариев Azure PowerShell для Azure Cosmos DB для API Core (SQL).

| |  |
|---|---|
|**Учетные записи Azure Cosmos**||
|[Создание учетной записи](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает учетную запись API SQL Azure Cosmos. |
|[Получение учетной записи](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает свойства учетной записи Azure Cosmos. |
|[Добавление региона](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает учетную запись Azure Cosmos и добавляет регион в список расположений. |
|[Изменение приоритетов отработки отказа](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменяет приоритет отработки отказа учетной записи Azure Cosmos с помощью активации отработки отказа вручную. |
|[Обновление тегов](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновляет теги для учетной записи Azure Cosmos. |
|[Получение ключей учетной записи](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает первичные и вторичные ключи учетной записи Azure Cosmos. |
|[Повторное создание ключей учетной записи](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Повторно создает первичные и вторичные ключи учетной записи Azure Cosmos. |
|[Вывод строк подключения](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает первичные и вторичные строки подключения учетной записи Azure Cosmos. |
|[Создание брандмауэра IP-адресов](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает брандмауэр IP-адресов для учетной записи Azure Cosmos. |
|[Удаление учетной записи Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Удаляет учетную запись Azure Cosmos. |
|**Базы данных Azure Cosmos**||
| [Создание базы данных](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает базу данных в учетной записи Azure Cosmos.|
| [Создание базы данных с общей пропускной способностью на уровне базы данных](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает базу данных Azure Cosmos с пропускной способностью на уровне базы данных, которую совместно используют содержащиеся в ней контейнеры.|
| [Вывод списка всех баз данных](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Выводит список всех баз данных в учетной записи Azure Cosmos.|
| [Получение базы данных](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Получает свойства базы данных Azure Cosmos.|
|**Контейнеры Azure Cosmos**||
| [Создание контейнера](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с выделенной пропускной способностью.|
| [Создание контейнера с общей пропускной способностью ](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с пропускной способностью, совместно используемой другими контейнерами в базе данных.|
| [Создание контейнера с политикой индексирования](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с настраиваемой политикой индексирования.|
| [Создание контейнера без политики индексирования](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с отключенной политикой индексирования.|
| [Создание контейнера с уникальными ключами и сроком жизни](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с настроенным ограничением уникального ключа и сроком жизни.|
| [Создание контейнера с разрешением конфликтов](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с политикой разрешения конфликтов, при которой сохраняются изменения, внесенные последними.|
| [Вывод списка всех контейнеров](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Выводит список всех контейнеров в базе данных Azure Cosmos.|
| [Получение контейнера](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Получает свойства для контейнера в базе данных Azure Cosmos.|
| [Удаление контейнера](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Удаляет контейнер в базе данных Azure Cosmos.|
|||
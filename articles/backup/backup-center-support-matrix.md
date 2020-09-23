---
title: Матрица поддержки для центра архивации
description: В этой статье перечислены сценарии, поддерживаемые центром архивации для каждого типа рабочей нагрузки.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8effc2514abf1cac55abc28b625b869810536baf
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997004"
---
# <a name="support-matrix-for-backup-center"></a>Матрица поддержки для центра архивации

Центр архивации предоставляет единую панель для предприятий, которая позволяет [управлять, отслеживать и анализировать резервные копии в нужном масштабе](backup-center-overview.md). В этой статье перечислены сценарии, поддерживаемые центром архивации для каждого типа рабочей нагрузки.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

| **Категория** | **Сценарий**  | **Поддерживаемые рабочие нагрузки**  | **Ограничения** |
| -------------| ------------- | ----------------------- |------------|
| Наблюдение   | Просмотреть все задания | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | <li> в течение 7 дней все задания доступны. <br> <li> Каждый фильтр/раскрывающийся список поддерживает не более 1000 элементов. Поэтому Центр архивации можно использовать для мониторинга максимум 1000 подписок и 1000 хранилищ между клиентами. |
| Наблюдение | Просмотреть все экземпляры резервного копирования | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | То же, что и выше |
| Наблюдение | Просмотреть все политики архивации | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | То же, что и выше |
| Наблюдение | Просмотреть все хранилища | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | То же, что и выше |
| Действия | Настройка резервного копирования | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [резервного копирования виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) и [базы данных Azure для PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) . |
| Действия | Восстановление экземпляра резервной копии | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [резервного копирования виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) и [базы данных Azure для PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) . |
| Действия | Создать хранилище | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [хранилища служб восстановления](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) . |
| Действия | Создание политики архивации | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [хранилища служб восстановления](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) . |
| Действия | Выполнение резервного копирования по запросу для экземпляра резервной копии | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [резервного копирования виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) и [базы данных Azure для PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) . |
| Действия | Завершение резервного копирования экземпляра резервной копии | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Дополнительные сведения см. в статье матрицы поддержки для [резервного копирования виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) и [базы данных Azure для PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) . |
| Аналитика | Просмотр отчетов по резервному копированию | <li> Виртуальная машина Azure <br><br> <li> SQL на виртуальной машине Azure <br><br> <li> SAP HANA на виртуальной машине Azure <br><br> <li> Файлы Azure <br><br> <li> System Center Data Protection Manager <br><br> <li> Агент Azure Backup (режим MARS) <br><br> <li> Azure Backup Server (MABS) | См. [Поддерживаемые сценарии резервного копирования отчетов](https://docs.microsoft.com/azure/backup/configure-reports#supported-scenarios) |
| Система управления | Просмотр и назначение встроенных и пользовательских политик Azure в категории "Архивация" | Недоступно | Недоступно |
| Система управления | Просмотр источников данных, не настроенных для архивации | <li> Виртуальная машина Azure <br><br> <li> Сервер базы данных Azure для PostgreSQL | Недоступно |

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

| **Категория** | **Сценарий**  |
|--------------|---------------|
| Наблюдение | Просмотр предупреждений в масштабе |
| Действия | Настройка параметров хранилища в масштабе |
| Действия | Выполнение задания по восстановлению между регионами из центра архивации |

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор матрицы поддержки для Azure Backup](https://docs.microsoft.com/azure/backup/backup-support-matrix)
* [Обзор матрицы поддержки для резервного копирования виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* [Ознакомьтесь со сведениями в матрице поддержки для резервного копирования сервера базы данных Azure для PostgreSQL](backup-azure-database-postgresql.md#support-matrix)

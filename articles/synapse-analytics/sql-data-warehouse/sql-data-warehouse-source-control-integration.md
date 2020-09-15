---
title: Интеграция системы управления версиями
description: Использование DevOps с базой данных корпоративного класса для пула SQL и интеграция платформенной функциональности системы управления версиями с помощью Azure Repos (Git и GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 84abfea39cb7311e7cd60346d936c08c28c334d4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441421"
---
# <a name="source-control-integration-for-sql-pool"></a>Интеграция системы управления версиями для пула SQL

Из этого руководства вы узнаете, как интегрировать проект базы данных SQL Server Data Tools (SSDT) с системой управления версиями.  Интеграция системы управления версиями — это первый шаг в создании конвейера непрерывной интеграции и развертывания для ресурса пула SQL в Azure Synapse Analytics.

## <a name="before-you-begin"></a>Перед началом

- Зарегистрируйтесь в [организации Azure DevOps](https://azure.microsoft.com/services/devops/).
- Выполните инструкции из руководства по [созданию и подключению](create-data-warehouse-portal.md).
- [Установите Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/).

## <a name="set-up-and-connect-to-azure-devops"></a>Настройка и подключение к Azure DevOps

1. В организации Azure DevOps создайте проект, в котором будет размещаться проект базы данных SSDT через репозиторий Azure Repos.

   ![Создание проекта](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Создание проекта")

2. Откройте Visual Studio и подключитесь к организации Azure DevOps и к проекту, созданному на шаге 1, выбрав **Управление подключениями**.

   ![Управление подключениями](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Управление подключениями")

3. Подключитесь к проекту, выбрав **Управление подключениями**, а затем **Подключиться к проекту**.
 ![Подключение 1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Подключение")


4. Найдите проект, созданный на шаге 1, и щелкните **Подключиться**.
![Подключение 2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Подключение")


3. Клонируйте репозиторий Azure DevOps из проекта на локальный компьютер.

   ![Клонирование репозиториев](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Клонирование репозиториев")

Дополнительные сведения о подключении проектов с помощью Visual Studio см. в статье [Подключение к проектам в Team Explorer](https://docs.microsoft.com/visualstudio/ide/connect-team-project?view=vs-2019). Инструкции по клонированию репозитория с помощью Visual Studio см. в статье [Клонирование существующего репозитория Git](https://docs.microsoft.com/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio). 

## <a name="create-and-connect-your-project"></a>Создание и подключение проекта

1. В Visual Studio создайте проект базы данных SQL Server с каталогом и локальным репозиторием Git в **локальном клонированном репозитории**.

   ![Создать новый проект](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Создание нового проекта")  

2. Щелкните правой кнопкой мыши пустой проект SQL и импортируйте хранилище данных в проект базы данных.

   ![Импорт проекта](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Импорт проекта")  

3. В Team Explorer в Visual Studio зафиксируйте все изменения в локальном репозитории Git.

   ![Фиксация](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. Теперь, когда изменения локально зафиксированы в клонированном репозитории, синхронизируйте и отправьте изменения в репозиторий Azure Repos в проекте Azure DevOps.

   ![Синхронизация и отправка (промежуточное хранение)](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Синхронизация и отправка (промежуточное хранение)")

   ![Синхронизация и отправка](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Синхронизация и отправка")  

## <a name="validation"></a>Проверка

1. Убедитесь, что изменения были отправлены в репозиторий Azure Repos, обновив столбец таблицы в проекте базы данных из Visual Studio SQL Server Data Tools (SSDT).

   ![Столбец проверки обновлений](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Столбец проверки обновлений")

2. Зафиксируйте и отправьте изменения из локального репозитория в свой репозиторий Azure Repos.

   ![Отправка изменений](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Отправка изменений")

3. Убедитесь, что изменение было отправлено в репозиторий Azure Repos.

   ![Проверка](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Проверка изменений")

4. (**Необязательно**.) Используйте расширение "Сравнение схем" и обновите изменения в целевом хранилище данных с помощью SSDT, чтобы определения объектов в репозитории в Azure Repos и локальном репозитории отражали изменения в хранилище данных.

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка для пула SQL](sql-data-warehouse-overview-develop.md)

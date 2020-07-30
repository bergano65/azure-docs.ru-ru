---
title: Краткое руководство. Использование Synapse Studio
description: В этом кратком руководстве вы увидите и узнаете, как легко запрашивать файлы различных типов с помощью Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8b35b281f6a70d5485004dc61563271132734a87
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374156"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Краткое руководство. Использование Synapse Studio (предварительная версия)

В этом кратком руководстве вы узнаете, как запрашивать файлы с помощью Synapse Studio.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Предварительные требования

[Создайте рабочую область Azure Synapse и связанную с ней учетную запись хранения](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Запуск Synapse Studio

На портале в рабочей области Azure Synapse щелкните **Запустить Synapse Studio**.

![Запуск Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Кроме того, Synapse Studio можно запустить, щелкнув [Azure Synapse Analytics](https://web.azuresynapse.net) и указав соответствующие значения для клиента, подписки и рабочей области.

## <a name="browse-storage-accounts"></a>Просмотр учетных записей хранения

После открытия Synapse Studio перейдите к **данным**, а затем разверните **учетные записи хранения** для просмотра учетной записи хранения в рабочей области.

![Обзор файлов в хранилище](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Вы можете создать новые папки и передать файлы с помощью ссылок на панели инструментов, чтобы упорядочить файлы.

## <a name="query-files-on-storage-account"></a>Файлы запросов в учетной записи хранения

> [!IMPORTANT]
> Чтобы иметь возможность запрашивать файлы, необходимо быть членом роли `Storage Blob Reader` в базовом хранилище. Узнайте, как [назначить для **Модуля чтения данных BLOB-объектов хранилища** или **Участника для данных BLOB-объектов хранилища** разрешения RBAC в службе хранилища Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role).

1. Отправьте некоторые файлы `PARQUET`.
2. Выберите один или несколько файлов, а затем создайте новый скрипт SQL или записную книжку Spark, чтобы просмотреть содержимое файлов. Если вы хотите создать записную книжку, необходимо создать [пул Apache Spark в рабочих областях Synapse](quickstart-create-apache-spark-pool-studio.md).

   ![Файлы запросов в хранилище](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Запустите созданный запрос или записную книжку, чтобы просмотреть содержимое файла.

   ![Просмотр содержимого файла](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Вы можете изменить запрос, чтобы выполнять фильтрацию и сортировку результатов. Узнайте о функциях языка, доступных в SQL по запросу, в статье [Функции Transact-SQL, поддерживаемые в Azure Synapse SQL](sql/overview-features.md).

## <a name="next-steps"></a>Дальнейшие действия

- Включение пользователей Azure AD для запроса файлов [путем назначения для **Модуля чтения данных BLOB-объектов хранилища** или **Участника для данных BLOB-объектов хранилища** разрешений RBAC в службе хранилища Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role)
- [Запросы к файлам в службе хранилища Azure с помощью SQL по запросу](sql/on-demand-workspace-overview.md)
- [Создание пула Apache Spark с помощью портала Azure](quickstart-create-apache-spark-pool-portal.md)
- [Создание отчета Power BI о файлах, хранящихся в службе хранилища Azure](sql/tutorial-connect-power-bi-desktop.md)

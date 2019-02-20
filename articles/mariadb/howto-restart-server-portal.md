---
title: Перезапуск сервера Базы данных Azure для MariaDB с помощью портала Azure
description: В этой статье объясняется, как перезапустить сервер Базы данных Azure для MariaDB с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 4b530ed2ffd32e2433a744b05aaab2219bf2b15a
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896594"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Перезапуск сервера Базы данных Azure для MariaDB с помощью портала Azure
В этой статье объясняется, как перезапустить сервер Базы данных Azure для MariaDB. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Время, необходимое для завершения перезапуска, зависит от процесса восстановления MariaDB. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер Базы данных Azure для MariaDB и база данных](./quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер MariaDB, выполните следующие действия.

1. На портале Azure выберите нужный сервер Базы данных Azure для MariaDB.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для MariaDB. Обзор. Кнопка перезапуска](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   ![База данных Azure для MariaDB. Подтверждение перезапуска ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для MariaDB. Статус перезапуска ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для MariaDB. Перезапуск завершен ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство Создание сервера Базы данных Azure для MySQL с помощью портала Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)
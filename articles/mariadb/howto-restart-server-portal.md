---
title: Перезапуск сервера-портал Azure — база данных Azure для MariaDB
description: В этой статье объясняется, как перезапустить сервер Базы данных Azure для MariaDB с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79534717"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Перезапуск сервера Базы данных Azure для MariaDB с помощью портала Azure
В этой статье объясняется, как перезапустить сервер Базы данных Azure для MariaDB. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Время, необходимое для завершения перезапуска, зависит от процесса восстановления MariaDB. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [Сервер базы данных Azure для MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер MariaDB, выполните следующие действия.

1. На портале Azure выберите нужный сервер Базы данных Azure для MariaDB.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для MariaDB. Обзор. Кнопка перезапуска](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   ![База данных Azure для MariaDB. Подтверждение перезапуска](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для MariaDB. Статус перезапуска](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для MariaDB. Перезапуск завершен](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство. Создание сервера базы данных Azure для MariaDB с помощью портал Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)
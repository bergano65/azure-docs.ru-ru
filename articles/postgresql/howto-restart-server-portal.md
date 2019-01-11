---
title: Перезапуск сервера Базы данных Azure для PostgreSQL с помощью портала Azure
description: В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2018
ms.openlocfilehash: 7d409db839f94e27ac036550c22302188f37cc90
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545881"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Перезапуск сервера Базы данных Azure для PostgreSQL с помощью портала Azure
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер и база данных Azure для PostgreSQL](quickstart-create-server-database-portal.md);

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер PostgreSQL, выполните следующие действия:

1. На портале Azure выберите нужный сервер службы "База данных Azure для PostgreSQL".

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для PostgreSQL — "Обзор" — кнопка "Восстановить"](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   ![База данных Azure для PostgreSQL — подтверждение восстановления ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для PostgreSQL — состояние перезапуска ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для PostgreSQL — перезапуск выполнен ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство Создание Базы данных Azure для PostgreSQL с помощью портала Azure](./quickstart-create-server-database-portal.md)
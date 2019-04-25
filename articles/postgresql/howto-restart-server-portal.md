---
title: Перезапуск сервера Базы данных Azure для PostgreSQL с помощью портала Azure
description: В этой статье описывается, как вы можете перезапустить базу данных Azure для сервера PostgreSQL с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: bf73120e462b740de5d2245f8a647896ac61f2c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420501"
---
# <a name="restart-azure-database-for-postgresql-server-using-the-azure-portal"></a>Перезапуск базы данных Azure для сервера PostgreSQL с помощью портала Azure
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Технические условия
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md);

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер PostgreSQL, выполните следующие действия:

1. В [портала Azure](https://portal.azure.com/), выберите свою базу данных Azure для сервера PostgreSQL.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для PostgreSQL — "Обзор" — кнопка "Восстановить"](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   ![База данных Azure для PostgreSQL — подтверждение восстановления](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для PostgreSQL — состояние перезапуска](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для PostgreSQL — перезапуск выполнен](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [как задать параметры в базе данных Azure для PostgreSQL](howto-configure-server-parameters-using-portal.md)
---
title: Перезагрузка сервера - Портал Azure - База данных Azure для PostgreS'L - Единый сервер
description: В этой статье описывается, как можно перезапустить базу данных Azure для PostgreS -L - Единый сервер с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770090"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Перезагрузка базы данных Azure для PostgreS-L - Единый сервер с помощью портала Azure
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md);

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер PostgreSQL, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com/)выберите базу данных Azure для сервера PostgreS'L.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для PostgreSQL — "Обзор" — кнопка "Восстановить"](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   ![База данных Azure для PostgreSQL — подтверждение восстановления](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для PostgreSQL — состояние перезапуска](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для PostgreSQL — перезапуск выполнен](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как устанавливать параметры в базе данных Azure для PostgreS'L](howto-configure-server-parameters-using-portal.md)
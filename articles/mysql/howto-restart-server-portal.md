---
title: Перезапуск Базы данных Azure для MySQL с помощью портала Azure
description: В этой статье объясняется, как перезапустить сервер в Базе данных Azure для MySQL с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/16/2018
ms.openlocfilehash: 83e862aea5b1f2de5a3f80970c2331fc9d81704e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540281"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Перезапуск Базы данных Azure для MySQL с помощью портала Azure
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для MySQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Время, необходимое для завершения перезапуска, зависит от процесса восстановления MySQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [Сервер и база данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер MySQL, выполните следующие действия:

1. На портале Azure выберите нужный сервер базы данных Azure для MySQL.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   ![База данных Azure для MySQL: кнопка "Перезапуск" на странице "Обзор"](./media/howto-restart-server-portal/2-server.png)

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера. 

   ![База данных Azure для MySQL — подтверждение перезапуска ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   ![База данных Azure для MySQL — состояние перезапуска ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Убедитесь, что сервер перезапущен.

   ![База данных Azure для MySQL — перезапуск выполнен ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по созданию сервера Базы данных Azure для MySQL с помощью портала Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
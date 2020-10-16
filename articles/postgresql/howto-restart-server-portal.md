---
title: Перезапуск сервера-портал Azure — база данных Azure для PostgreSQL — один сервер
description: В этой статье описывается, как можно перезапустить базу данных Azure для PostgreSQL-Single Server с помощью портал Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907382"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Перезапустите базу данных Azure для PostgreSQL — один сервер с помощью портал Azure
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md);

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер PostgreSQL, выполните следующие действия:

1. В [портал Azure](https://portal.azure.com/)выберите сервер базы данных Azure для PostgreSQL.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="База данных Azure для PostgreSQL — &quot;Обзор&quot; — кнопка &quot;Восстановить&quot;":::

3. Нажмите кнопку **Да**, чтобы подтвердить перезапуск сервера.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="База данных Azure для PostgreSQL — &quot;Обзор&quot; — кнопка &quot;Восстановить&quot;":::

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="База данных Azure для PostgreSQL — &quot;Обзор&quot; — кнопка &quot;Восстановить&quot;":::

5. Убедитесь, что сервер перезапущен.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="База данных Azure для PostgreSQL — &quot;Обзор&quot; — кнопка &quot;Восстановить&quot;":::

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о [настройке параметров в базе данных Azure для PostgreSQL](howto-configure-server-parameters-using-portal.md)
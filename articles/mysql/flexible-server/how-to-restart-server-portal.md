---
title: Перезапуск сервера-портал Azure — гибкий сервер для базы данных Azure для MySQL
description: В этой статье описывается, как можно перезапустить гибкий сервер базы данных Azure для MySQL с помощью портал Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: c44e1da46b969b2d359a225e9d310160ce2092ce
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681683"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Перезапуск базы данных Azure для гибкого сервера MySQL с помощью портал Azure
В этом разделе описывается, как можно перезапустить гибкий сервер базы данных Azure для MySQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Время, необходимое для завершения перезапуска, зависит от процесса восстановления MySQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [Гибкий сервер базы данных Azure для MySQL](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Перезапуск сервера

Чтобы перезапустить сервер MySQL, выполните следующие действия:

1. В портал Azure выберите базу данных Azure для гибкого сервера MySQL.

2. На панели инструментов страницы **Обзор** сервера нажмите кнопку **Восстановить** .

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="База данных Azure для MySQL: кнопка &quot;Перезапуск&quot; на странице &quot;Обзор&quot;":::

3. Нажмите кнопку **Да** , чтобы подтвердить перезапуск сервера.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="База данных Azure для MySQL: кнопка &quot;Перезапуск&quot; на странице &quot;Обзор&quot;":::

4. Убедитесь, что состояние сервера изменилось на "Идет перезапуск".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="База данных Azure для MySQL: кнопка &quot;Перезапуск&quot; на странице &quot;Обзор&quot;":::

5. Убедитесь, что сервер перезапущен.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="База данных Azure для MySQL: кнопка &quot;Перезапуск&quot; на странице &quot;Обзор&quot;":::

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство. Создание базы данных Azure для гибкого сервера MySQL с помощью портал Azure](quickstart-create-server-portal.md)

---
title: Restart-портал Azure. база данных Azure для PostgreSQL — гибкий сервер
description: В этой статье описывается, как выполнять операции перезапуска в базе данных Azure для PostgreSQL с помощью портал Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936935"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Перезапуск базы данных Azure для PostgreSQL-гибкого сервера

> [!IMPORTANT]
> Гибкий сервер Базы данных Azure для PostgreSQL предоставляется в режиме предварительной версии

В этой статье приводятся пошаговые инструкции по выполнению перезапуска гибкого сервера. Эта операция полезна для применения любых изменений статических параметров, требующих перезапуска сервера базы данных. Процедура идентична для серверов, настроенных с высокой доступностью, избыточными в пределах зоны. 

> [!IMPORTANT]
> При настройке с высоким уровнем доступности основной и резервный серверы перезапускаются одновременно.

## <a name="pre-requisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

-   Необходим гибкий сервер.

## <a name="restart-your-flexible-server"></a>Перезапуск гибкого сервера

Выполните следующие действия, чтобы перезапустить гибкий сервер.

1.  В [портал Azure](https://portal.azure.com/)выберите гибкий сервер, который требуется перезапустить.

2.  Щелкните **Обзор** на левой панели и нажмите кнопку **перезапустить**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Перезапустить выделенный фрагмент":::

3.  Появится всплывающее сообщение с подтверждением.

4.  Нажмите кнопку **Да** , если хотите продолжить.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Перезапустить выделенный фрагмент":::
 
6.  Будет отображено уведомление о том, что операция перезапуска запущена.

## <a name="next-steps"></a>Дальнейшие шаги

-   Сведения о [непрерывности бизнес-процессов](./concepts-business-continuity.md)
-   Сведения о [высокой доступности с избыточностью зоны](./concepts-high-availability.md)

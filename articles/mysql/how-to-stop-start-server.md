---
title: Окончание или запуск — портал Azure — сервер базы данных Azure для MySQL
description: В этой статье описывается, как останавливаться и запускать операции в базе данных Azure для MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 047a24133dfdf68e2176b20cf31a871d11f0d4f9
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241163"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Завершение работы и запуск базы данных Azure для MySQL

> [!IMPORTANT]
> В настоящее время доступна общедоступная Предварительная версия функции "завершение работы и запуск" для базы данных Azure для MySQL.

В этой статье приводятся пошаговые инструкции по выполнению и запуску одного сервера.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

-   У вас должна быть база данных Azure для MySQL Single Server.

> [!NOTE]
> См. ограничение на использование команды " [прерывать/запустить](concepts-servers.md#limitations-of-stopstart-operation) ".

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Как отключить или запустить базу данных Azure для MySQL с помощью портал Azure

### <a name="stop-a-running-server"></a>Завершение работы сервера

1.  В [портал Azure](https://portal.azure.com/)выберите сервер MySQL, который требуется отключить.

2.  На странице **Обзор** нажмите кнопку " **Закрыть** " на панели инструментов.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Завершение сервера базы данных Azure для MySQL":::

    > [!NOTE]
    > После остановки сервера другие операции управления недоступны для одного сервера.

### <a name="start-a-stopped-server"></a>Запуск остановленного сервера

1.  В [портал Azure](https://portal.azure.com/)выберите один сервер, который требуется запустить.

2.  На странице **Обзор** нажмите кнопку **Запуск** на панели инструментов.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Завершение сервера базы данных Azure для MySQL":::

    > [!NOTE]
    > После запуска сервера все операции управления теперь доступны для одного сервера.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Как отключить или запустить базу данных Azure для MySQL с помощью интерфейса командной строки

### <a name="stop-a-running-server"></a>Завершение работы сервера

1.  В [портал Azure](https://portal.azure.com/)выберите сервер MySQL, который требуется отключить.

2.  На странице **Обзор** нажмите кнопку " **Закрыть** " на панели инструментов.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > После остановки сервера другие операции управления недоступны для одного сервера.

### <a name="start-a-stopped-server"></a>Запуск остановленного сервера

1.  В [портал Azure](https://portal.azure.com/)выберите один сервер, который требуется запустить.

2.  На странице **Обзор** нажмите кнопку **Запуск** на панели инструментов.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > После запуска сервера все операции управления теперь доступны для одного сервера.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте [, как создавать оповещения по метрикам](howto-alert-on-metric.md).

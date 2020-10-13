---
title: Перенос минимального времени простоя в базу данных Azure для PostgreSQL — один сервер
description: В этой статье описывается, как выполнить простой перенос базы данных PostgreSQL в базу данных Azure для PostgreSQL-Single Server с помощью Azure Database Migration Service.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1f1af53388f177dc348c5cb805ef8e6fbe9f9436
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710827"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Перенос минимального времени простоя в базу данных Azure для PostgreSQL — один сервер
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

Благодаря новой возможности **непрерывной синхронизации**, реализованной в службе [Azure Database Migration Service](https://aka.ms/get-dms) (DMS), вы можете переносить базы данных PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя. Эта функция позволяет сократить время простоя при работе приложения.

## <a name="overview"></a>Обзор
Azure Database Migration Service выполняет начальную загрузку локальных данных в Базу данных Azure для PostgreSQL и непрерывно синхронизирует все новые транзакции с Azure. Все это время приложение продолжает работать. Когда данные поступят в целевую службу Azure, остановите приложение на короткое время (минимальное время простоя), подождите, пока последний пакет данных (с момента остановки приложения до его фактической недоступности для принятия нового трафика) будет передан в целевую службу, а затем обновите строку подключения, чтобы она указывала на Azure. По завершении приложение станет доступно в Azure.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Непрерывная синхронизация с Azure Database Migration Service":::

## <a name="next-steps"></a>Дальнейшие шаги
- Посмотрите видео [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Усовершенствование приложений с помощью Microsoft Azure), в котором показано, как переносить приложения PostgreSQL в Базу данных Azure для PostgreSQL.
- Ознакомьтесь с руководством [Миграция PostgreSQL в Базу данных Azure для PostgreSQL через Интернет с помощью Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).

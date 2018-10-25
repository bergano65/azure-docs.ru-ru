---
title: Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
description: В этой статье объясняется, как с помощью Azure Database Migration Service перенести базу данных PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093839"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
Благодаря новой возможности **непрерывной синхронизации**, реализованной в службе [Azure Database Migration Service](https://aka.ms/get-dms) (DMS), вы можете переносить базы данных PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя. Эта функция позволяет сократить время простоя при работе приложения.

## <a name="overview"></a>Обзор
DMS выполняет начальную загрузку локальных данных в Базу данных Azure для PostgreSQL и непрерывно синхронизирует все новые транзакции с Azure. Все это время приложение продолжает работать. Когда данные поступят в целевую службу Azure, остановите приложение на короткое время (минимальное время простоя), подождите, пока последний пакет данных (с момента остановки приложения до его фактической недоступности для принятия нового трафика) будет передан в целевую службу, а затем обновите строку подключения, чтобы она указывала на Azure. По завершении приложение станет доступно в Azure.

![Непрерывная синхронизация с Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

Сейчас функция миграции источников PostgreSQL в DMS доступна в режиме предварительной версии. Если вы хотите попробовать перенести рабочие нагрузки PostgreSQL с помощью службы, зарегистрируйтесь на странице [предварительной версии](https://aka.ms/dms-preview) Azure DMS. Ваши отзывы очень важны для нас. Они помогают нам улучшать службу.

## <a name="next-steps"></a>Дополнительная информация
- Посмотрите видео [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Усовершенствование приложений с помощью Microsoft Azure), в котором показано, как переносить приложения PostgreSQL в Базу данных Azure для PostgreSQL.
- Ознакомьтесь с руководством [Миграция PostgreSQL в Базу данных Azure для PostgreSQL через Интернет с помощью Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).

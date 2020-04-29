---
title: Миграция с минимальным временем простоя — база данных Azure для MySQL
description: В этой статье объясняется, как с помощью Azure Database Migration Service перенести базу данных MySQL в Базу данных Azure для MySQL с минимальным временем простоя.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063340"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Миграция в службу "База данных Azure для MySQL" с минимальным простоем
Благодаря новой возможности **непрерывной синхронизации** в [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) вы можете переносить базы данных MySQL в Базу данных Azure для MySQL с минимальным временем простоя. Эта функция позволяет сократить время простоя при работе приложения.

## <a name="overview"></a>Обзор
Azure Database Migration Service выполняет начальную загрузку локальных баз данных в Базу данных Azure для MySQL и непрерывно синхронизирует все новые транзакции с Azure во время работы приложения. Когда данные поступят в целевую службу Azure, остановите приложение на короткое время (минимальное время простоя), подождите, пока последний пакет данных (с момента остановки приложения до его фактической недоступности для принятия нового трафика) будет передан в целевую службу, а затем обновите строку подключения, чтобы она указывала на Azure. По завершении приложение станет доступно в Azure.

![Непрерывная синхронизация с Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Дальнейшие шаги
- Просмотрите видео [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Простой перенос приложений MySQL и PostgreSQL в управляемую службу Azure), в котором демонстрируется миграция приложений MySQL в Базу данных Azure для MySQL.
- Ознакомьтесь с руководством [Интерактивная миграция MySQL в Базу данных Azure для MySQL с помощью DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).

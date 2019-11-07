---
title: Синхронизация данных из Базы данных SQL Azure для пограничных вычислений с помощью Синхронизации данных SQL | Документация Майкрософт
description: Сведения о синхронизации данных в Базе данных SQL Azure для пограничных вычислений с помощью Синхронизации данных SQL Azure
keywords: база данных sql для пограничных вычислений, синхронизация данных из базы данных sql для пограничных вычислений, синхронизация данных в базе данных sql для пограничных вычислений
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509198"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Руководство по Синхронизация данных из Базы данных SQL для пограничных вычислений с Базой данных SQL Azure с помощью Синхронизации данных SQL

В этом руководстве вы узнаете, как использовать *группу синхронизации* технологии "Синхронизация данных SQL" для добавочной синхронизации данных из Базы данных SQL Azure для пограничных вычислений с Базой данных SQL Azure. Синхронизация данных SQL — это служба, созданная на основе Базы данных SQL Azure, которая позволяет в двунаправленном режиме синхронизировать выбранные данные в нескольких базах данных SQL и экземплярах SQL Server. Дополнительные сведения о Синхронизации данных SQL Azure см. в статье [Синхронизация данных SQL Azure](../sql-database/sql-database-sync-data.md).

Так как База данных SQL Azure для пограничных вычислений основана на последней версии [ядра СУБД Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), любой механизм синхронизации данных, применимый к локальному экземпляру SQL Server, также можно использовать для синхронизации данных с экземпляром Базы данных SQL для пограничных вычислений, выполняющимся на пограничном устройстве, или из него.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется компьютер с ОС Windows, на котором настроен [SQL Azure Data Sync Agent](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Перед началом работы

* Создайте Базу данных SQL Azure. Сведения о создании Базы данных SQL Azure с помощью портала Azure см. в статье [Создание отдельной базы данных в Базе данных SQL Azure](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Создайте таблицы и другие необходимые объекты в экземпляре Базы данных SQL Azure.

* Создайте необходимые таблицы и объекты в экземпляре Базы данных SQL Azure для пограничных вычислений. Дополнительные сведения см. в статье [Использование пакетов DAC Базы данных SQL с Базой данных SQL для пограничных вычислений](stream-analytics.md).

* Зарегистрируйте экземпляр Базы данных SQL Azure для пограничных вычислений в агенте SQL Azure Data Sync Agent. Дополнительные сведения см. в статье [Добавление локальной базы данных SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Синхронизация данных между Базой данных SQL Azure и Базой данных SQL для пограничных вычислений

Настройка синхронизации между Базой данных SQL Azure и Базой данных SQL для пограничных вычислений с помощью Синхронизации данных SQL производится в три основных шага.  

1. Создайте группу синхронизации на портале Azure. Сведения о создании группы синхронизации см. в статье [Создание группы синхронизации на портале Azure](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Одну и ту же *центральную* базу данных можно использовать для создания нескольких групп синхронизации, чтобы синхронизировать данные из разных экземпляров Базы данных SQL для пограничных вычислений с одной или несколькими Базами данных SQL в Azure.

2. Добавьте участников в группу синхронизации. Сведения о добавлении участников в группу синхронизации см. в статье [Добавление участников в группу синхронизации данных SQL](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Настройте группу синхронизации, выбрав таблицы, которые будут участвовать в синхронизации. Сведения о настройке группы синхронизации см. в статье [Настройка группы синхронизации](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

После выполнения описанных выше действий вы получите группу синхронизации, которая включает в себя Базу данных SQL Azure и экземпляр Базы данных SQL для пограничных вычислений.

Дополнительные сведения о Синхронизации данных SQL см. в следующих статьях.

* Сведения об [агенте синхронизации данных для синхронизации данных SQL Azure](../sql-database/sql-database-data-sync-agent.md).

* [Рекомендации по синхронизации данных SQL](../sql-database/sql-database-best-practices-data-sync.md) и инструкции по [устранению неполадок с синхронизацией данных SQL](../sql-database/sql-database-troubleshoot-data-sync.md).

* [Мониторинг синхронизации данных SQL с помощью журналов Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* Сведения об обновлении схемы синхронизации с помощью [Transact-SQL](../sql-database/sql-database-update-sync-schema.md) или [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md).

## <a name="next-steps"></a>Дополнительная информация

* [Использование PowerShell для синхронизации данных между Базой данных SQL Azure и Базой данных SQL Azure для пограничных вычислений](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md) Замените в руководстве сведения о базе данных *OnPremiseServer* сведениями о Базе данных SQL Azure для пограничных вычислений.

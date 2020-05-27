---
title: Синхронизация данных из SQL Azure для пограничных вычислений (предварительная версия) с помощью Синхронизации данных SQL
description: Сведения о синхронизации данных из SQL Azure для пограничных вычислений (предварительная версия) с помощью Синхронизации данных SQL Azure
keywords: SQL Azure для пограничных вычислений, синхронизация данных из SQL Azure для пограничных вычислений, синхронизация данных SQL Azure для пограничных вычислений
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6bcdfc3eb09b6c5ed4f8dde4d48a34bee8746e1e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593448"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Руководство по синхронизации данных из SQL Azure для пограничных вычислений с Базой данных SQL Azure с помощью Синхронизации данных SQL

В этом учебнике вы узнаете, как с помощью *группы синхронизации* технологии "Синхронизация данных SQL Azure" выполнить добавочную синхронизацию данных из SQL Azure для пограничных вычислений с Базой данных SQL Azure. Синхронизация данных SQL — это служба, созданная на основе Базы данных SQL Azure, которая позволяет в двунаправленном режиме синхронизировать выбранные данные в нескольких базах данных SQL и экземплярах SQL Server. Дополнительные сведения о Синхронизации данных SQL см. в [этой статье](../sql-database/sql-database-sync-data.md).

Так как служба "SQL Azure для пограничных вычислений" основана на последней версии [ядра СУБД SQL Server](/sql/sql-server/sql-server-technical-documentation/), любой механизм синхронизации данных, применимый к локальному экземпляру SQL Server, также можно использовать для синхронизации данных с экземпляром SQL для пограничных вычислений, выполняющимся на пограничном устройстве, или из него.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется компьютер с ОС Windows, на котором настроен [Data Sync Agent для Синхронизации данных SQL Azure](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Перед началом

* Создайте базу данных SQL Azure. Сведения о создании Базы данных SQL Azure с помощью портала Azure см. в [этой статье](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Создайте таблицы и другие необходимые объекты в экземпляре Базы данных SQL Azure.

* Создайте необходимые таблицы и объекты в развертывании SQL Azure для пограничных вычислений. Дополнительные сведения см. в статье [Использование пакетов DAC Базы данных SQL с SQL Azure для пограничных вычислений](stream-analytics.md).

* Зарегистрируйте экземпляр SQL Azure для пограничных вычислений с помощью Data Sync Agent для Синхронизации данных SQL Azure. Дополнительные сведения см. в статье [Добавление локальной базы данных SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Синхронизация данных между базой данных SQL Azure и SQL Azure для пограничных вычислений

Настройка синхронизации между базой данных SQL Azure и экземпляром SQL Azure для пограничных вычислений с помощью Синхронизации данных SQL производится в три основных шага.  

1. Создайте группу синхронизации на портале Azure. Дополнительные сведения см. в разделе [о создании группы синхронизации](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Вы можете использовать одну *центральную* базу данных для создания нескольких групп синхронизации, чтобы синхронизировать данные из разных экземпляров SQL Azure для пограничных вычислений с одной или несколькими базами данных SQL в Azure.

2. Добавьте члены в группу синхронизации. Дополнительные сведения см. в разделе [Добавление членов синхронизации](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Настройте группу синхронизации, выбрав таблицы, которые будут участвовать в синхронизации. Дополнительные сведения см. в разделе [Настройка группы синхронизации](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

После выполнения описанных выше действий вы получите группу синхронизации, которая включает в себя базу данных SQL Azure и экземпляр SQL Azure для пограничных вычислений.

Дополнительные сведения о Синхронизации данных SQL см. в этих статьях:

* Сведения об [агенте синхронизации данных для синхронизации данных SQL Azure](../sql-database/sql-database-data-sync-agent.md).

* [Рекомендации по синхронизации данных SQL](../sql-database/sql-database-best-practices-data-sync.md) и инструкции по [устранению неполадок с синхронизацией данных SQL](../sql-database/sql-database-troubleshoot-data-sync.md).

* [Мониторинг синхронизации данных SQL с помощью журналов Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* Сведения об обновлении схемы синхронизации с помощью [Transact-SQL](../sql-database/sql-database-update-sync-schema.md) или [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Использование PowerShell для синхронизации данных между Базой данных SQL и SQL Azure для пограничных вычислений](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Замените в этом учебнике сведения о базе данных `OnPremiseServer` сведениями о SQL Azure для пограничных вычислений.

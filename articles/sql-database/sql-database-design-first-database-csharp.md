---
title: Проектирование первой базы данных SQL Azure на языке C# | Документация Майкрософт
description: Узнайте, как спроектировать свою первую базу данных SQL Azure и подключиться к ней с помощью программы на языке C#, используя ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727171"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Руководство. Проектирование базы данных SQL Azure и подключение к ней с помощью C# и ADO.NET

База данных SQL Azure — это реляционная база данных как услуга (DBaaS) в Microsoft Cloud (Azure). Из этого руководства вы узнаете, как с помощью портала Azure и ADO.NET с Visual Studio выполнить следующие действия:

> [!div class="checklist"]
> * создать базу данных на портале Azure;
> * настроить правило брандмауэра на уровне сервера на портале Azure;
> * подключиться к базе данных с помощью ADO.NET и Visual Studio;
> * создать таблицы с помощью ADO.NET;
> * вставить, обновить и удалить данные с помощью ADO.NET;
> * выполнить запрос данных с помощью ADO.NET.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Установленная среда [Visual Studio 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали об основных задачах базы данных, таких как создание базы данных и таблиц, подключение к базе данных, загрузка данных и выполнение запросов. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных
> * Настройка правила брандмауэра.
> * подключаться к базе данных с помощью [Visual Studio и C#](sql-database-connect-query-dotnet-visual-studio.md);
> * создание таблиц.
> * Вставка, обновление, удаление и запрашивание данных.

Перейдите к следующему руководству, чтобы узнать о переносе данных.

> [!div class="nextstepaction"]
> [Перенос баз данных из SQL Server в Базу данных SQL Azure в автономном режиме с помощью DMS](../dms/tutorial-sql-server-to-azure-sql.md)

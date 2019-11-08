---
title: Создание отдельной базы данных
description: Из этой статьи вы узнаете, как создать отдельную базу данных в Базе данных SQL Azure и отправить к ней запросы с помощью портала Azure, PowerShell и Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 482fc3f08fb986908efcab83a82338ced36ee689
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821004"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Краткое руководство. Создание отдельной базы данных в Базе данных SQL Azure с помощью портала Azure, PowerShell и Azure CLI

Создание [отдельной базы данных](sql-database-single-database.md) — это самый быстрый и простой вариант развертывания для создания базы данных в Базе данных SQL Azure. В этом кратком руководстве описано, как создать отдельную базу данных и отправить к ней запросы с помощью портала Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/). 

Для выполнения всех шагов в этом кратком руководстве войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Создание отдельной базы данных

Отдельную базу данных можно создавать в рамках уровня подготовленных или бессерверных вычислений.

- Отдельная база данных на уровне подготовленных вычислений имеет определенное количество предварительно выделенных вычислительных ресурсов, в том числе объем памяти и циклов ЦП, для которых используется одна из двух [моделей приобретения](sql-database-purchase-models.md).
- Отдельная база данных на уровне бессерверных вычислений имеет широкий спектр автоматически масштабирующихся вычислительных ресурсов, в том числе объем памяти и циклов ЦП. Такая база данных доступна только в рамках [моделей приобретения на основе виртуальных ядер](sql-database-service-tiers-vcore.md).

При создании отдельной базы данных можете также определить [Сервер Базы данных SQL](sql-database-servers.md), чтобы управлять им и разместить его в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в указанном регионе.

> [!NOTE]
> В этом кратком руководстве используется [модель приобретения на основе виртуальных ядер](sql-database-service-tiers-vcore.md). Также доступна [модель приобретения на основе единиц DTU](sql-database-service-tiers-DTU.md).

Чтобы создать базу данных, содержащую образец данных AdventureWorksLT, выполните следующие действия.

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Выполнение запросов к базе данных

Завершив создание базы данных, воспользуйтесь встроенным средством запросов на портале Azure, чтобы подключиться к этой базе данных и выполнить запрос данных.

1. На странице **База данных SQL** для этой базы данных найдите и выберите элемент **Редактор запросов (предварительная версия)** в меню слева.

   ![Вход в редактор запросов](./media/sql-database-get-started-portal/query-editor-login.png)

2. Введите учетные данные и щелкните **ОК**.
3. Введите следующий запрос в области **Редактор запросов**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Щелкните **Выполнить** и просмотрите результаты запроса в области **Результаты**.

   ![Результаты в редакторе запросов](./media/sql-database-get-started-portal/query-editor-results.png)

5. Закройте страницу **редактора запросов** и щелкните **ОК**, когда увидите запрос на отмену несохраненных изменений.

## <a name="clean-up-resources"></a>Очистка ресурсов

Сохраните эту группу ресурсов, сервер базы данных и отдельную базу данных, чтобы перейти к [дальнейшим действиям](#next-steps). Далее показано, как установить подключение и выполнить запрос к базе данных с помощью разных методов.

Когда вы завершите работу с этими ресурсами, удалите их следующим образом.

1. На портале Azure в меню слева щелкните **Группы ресурсов**, а затем выберите **myResourceGroup**.
2. На странице группы ресурсов щелкните **Удалить группу ресурсов**.
3. Введите *myResourceGroup* в поле и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

- Создайте правило брандмауэра на уровне сервера, чтобы подключиться к отдельной базе данных с помощью локальных или удаленных средств. Подробные сведения см. в разделе [Создание правила брандмауэра на уровне сервера](sql-database-server-level-firewall-rule.md).
- Создав правило брандмауэра на уровне сервера, вы сможете [устанавливать подключение и выполнять запросы к базе данных](sql-database-connect-query.md) с помощью разных средств и языков.
  - [подключиться и создать запрос с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md);
  - [подключиться и создать запрос с помощью Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json).
- Чтобы создать отдельную базу данных на уровне подготовленных вычислений с помощью Azure CLI, ознакомьтесь с [примерами Azure CLI](sql-database-cli-samples.md).
- Чтобы создать отдельную базу данных на уровне подготовленных вычислений с помощью Azure PowerShell, ознакомьтесь с [примерами Azure PowerShell](sql-database-powershell-samples.md).
- Чтобы создать отдельную базу данных на уровне бессерверных вычислений с помощью Azure Powershell, ознакомьтесь с разделом [Создание бессерверной базы данных](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

---
title: Использование данных Azure Blockchain Workbench с SQL Server Management Studio
description: Сведения о подключении к базе данных SQL Azure Blockchain Workbench из SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 32606f988152b061fa1923c409207b2a650e64ba
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332461"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Использование данных Azure Blockchain Workbench с SQL Server Management Studio

Microsoft SQL Server Management Studio позволяет быстро записывать и тестировать запросы к базе данных SQL Azure Blockchain Workbench. В этом разделе содержится пошаговое руководство по подключению к базе данных SQL Azure Blockchain Workbench из SQL Server Management Studio.

## <a name="prerequisites"></a>Предварительные требования

* Скачайте [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Подключение SQL Server Management Studio к данным в Azure Blockchain Workbench

1. Откройте SQL Server Management Studio и выберите **Подключение**.
2. Выберите **Ядро СУБД**.

    ![Ядро СУБД](./media/data-sql-management-studio/database-engine.png)

3. В диалоговом окне **Подключение к серверу** введите имя сервера и учетные данные базы данных.

    Если вы используете учетные данные, созданные в процессе развертывания Azure Blockchain Workbench, именем пользователя будет **dbadmin**, а в качестве пароля будет использован пароль, указанный во время развертывания.

    ![Введение учетных данных SQL](./media/data-sql-management-studio/sql-creds.png)

 4. В SQL Server Management Studio показан список баз данных, представлений баз данных и хранимые процедуры в базе данных Azure Blockchain Workbench.

    ![Список баз данных](./media/data-sql-management-studio/db-list.png)

5. Чтобы просмотреть данные, связанные с любым из представлений базы данных, можно автоматически создать инструкцию select, выполнив следующие действия.
6. Щелкните правой кнопкой мыши любое из представлений базы данных в обозревателе объектов.
7. Выберите **Script View as** (Представление скрипта как).
8. Выберите **SELECT to** (ВЫБРАТЬ для).
9. Выберите **New Query Editor Window** (Окно редактирования нового запроса).
10. Запрос можно создать, выбрав **Новый запрос**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)

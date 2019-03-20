---
title: Использование данных Azure Blockchain Workbench в Microsoft Power BI
description: Узнайте, как загружать и просматривать данные базы данных SQL Azure Blockchain Workbench в Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e12af686a450d39332c37700b9a14b9eb620307
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530898"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Использование данных Azure Blockchain Workbench с помощью Microsoft Power BI

Microsoft Power BI предоставляет возможность легко создавать важные отчеты из баз данных SQL с помощью Power BI Desktop, а затем публиковать их на сайте [https://www.powerbi.com](https://www.powerbi.com).

В этой статье содержится пошаговое руководство по подключению к базе данных SQL Azure Blockchain Workbench из PowerBI Desktop, созданию отчета и его развертыванию на сайте powerbi.com.

## <a name="prerequisites"></a>Технические условия

* Загрузите [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Подключение к данным Power BI в Azure Blockchain Workbench

1.  Откройте Power BI Desktop.
2.  Выберите **Получение данных**.

    ![Получение данных](./media/data-powerbi/get-data.png)
3.  Выберите **SQL Server** в списке типов источников данных.

4.  В диалоговом окне укажите имя сервера и базы данных. Укажите, следует ли импортировать данные или выполнять **DirectQuery**. Нажмите кнопку **ОК**.

    ![Выбор SQL Server](./media/data-powerbi/select-sql.png)

5.  Укажите учетные данные базы данных для доступа к Azure Blockchain Workbench. Выберите **База данных** и введите свои учетные данные.

    Если вы используете учетные данные, созданные в процессе развертывания Azure Blockchain Workbench, именем пользователя будет **dbadmin**, а в качестве пароля будет использован пароль, указанный во время развертывания.

    ![Настройки SQL DB](./media/data-powerbi/db-settings.png)

6.  После соединения с базой данных отобразится диалоговое окно **Навигатор**, в котором содержатся таблицы и представления, доступные в базе данных. Представления предназначены для создания отчетов. Их названия начинаются с префикса **vw**.

    ![Навигатор](./media/data-powerbi/navigator.png)

7.  Выберите представления, которые нужно включить. В демонстрационных целях мы включили **vwContractAction**, который предоставляет подробные сведения о действиях, выполняемых в контракте.

    ![Выбор представлений](./media/data-powerbi/select-views.png)

Теперь вы можете создавать и публиковать отчеты как обычно, с помощью Power BI.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)
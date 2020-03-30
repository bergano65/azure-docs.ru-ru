---
title: Использование данных Azure Blockchain Workbench в Microsoft Power BI
description: Узнайте, как загружать и просматривать данные базы данных SQL Azure Blockchain Workbench в Microsoft Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324796"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Использование данных Azure Blockchain Workbench с помощью Microsoft Power BI

Microsoft Power BI обеспечивает возможность легко генерировать мощные отчеты из баз данных [https://www.powerbi.com](https://www.powerbi.com)S'L DB с помощью Power BI Desktop, а затем публиковать их.

В этой статье содержится пошаговое руководство по подключению к базе данных SQL Azure Blockchain Workbench из PowerBI Desktop, созданию отчета и его развертыванию на сайте powerbi.com.

## <a name="prerequisites"></a>Предварительные требования

* Скачать [Power BI Desktop](https://aka.ms/pbidesktopstore).

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

    ![Navigator](./media/data-powerbi/navigator.png)

7.  Выберите представления, которые нужно включить. В демонстрационных целях мы включили **vwContractAction**, который предоставляет подробные сведения о действиях, выполняемых в контракте.

    ![Выбор представлений](./media/data-powerbi/select-views.png)

Теперь вы можете создавать и публиковать отчеты как обычно, с помощью Power BI.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)
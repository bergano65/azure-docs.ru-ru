---
title: Использование данных Azure Blockchain Workbench в Microsoft Power BI
description: Узнайте, как загружать и просматривать данные базы данных SQL Azure Blockchain Workbench в Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: article
ms.reviewer: sunri
ms.openlocfilehash: 4245603fee5b3d24488426c3dc2d026a3c0d7848
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082532"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Использование данных Azure Blockchain Workbench с помощью Microsoft Power BI

Microsoft Power BI предоставляет возможность легко создавать эффективные отчеты из баз данных SQL с помощью Power BI Desktop, а затем публиковать их в [https://www.powerbi.com](https://www.powerbi.com).

В этой статье содержится пошаговое руководство по подключению к базе данных SQL Azure Blockchain Workbench из PowerBI Desktop, созданию отчета и его развертыванию на сайте powerbi.com.

## <a name="prerequisites"></a>Предварительные условия

* Скачайте [Power BI Desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Подключение к данным Power BI в Azure Blockchain Workbench

1.  Откройте Power BI Desktop.
2.  Выберите **Получение данных**.

    ![Получение данных](./media/data-powerbi/get-data.png)
3.  Выберите **SQL Server** в списке типов источников данных.

4.  В диалоговом окне укажите имя сервера и базы данных. Укажите, следует ли импортировать данные или выполнять **DirectQuery**. Нажмите кнопку **OK**.

    ![Выбор SQL Server](./media/data-powerbi/select-sql.png)

5.  Укажите учетные данные базы данных для доступа к Azure Blockchain Workbench. Выберите **База данных** и введите свои учетные данные.

    Если вы используете учетные данные, созданные в процессе развертывания Azure Blockchain Workbench, именем пользователя будет **dbadmin**, а в качестве пароля будет использован пароль, указанный во время развертывания.

    ![Настройки SQL DB](./media/data-powerbi/db-settings.png)

6.  После соединения с базой данных отобразится диалоговое окно **Навигатор**, в котором содержатся таблицы и представления, доступные в базе данных. Представления предназначены для создания отчетов. Их названия начинаются с префикса **vw**.

    ![Navigator](./media/data-powerbi/navigator.png)

7.  Выберите представления, которые нужно включить. В демонстрационных целях мы включили **vwContractAction**, который предоставляет подробные сведения о действиях, выполняемых в контракте.

    ![Выбор представлений](./media/data-powerbi/select-views.png)

Теперь вы можете создавать и публиковать отчеты как обычно, с помощью Power BI.

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)
---
title: Как создать запрос в службу поддержки
description: Порядок создания запроса в службу поддержки для хранилища данных SQL Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717839"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Создание запроса в службу поддержки для хранилища данных SQL
Если у вас возникли проблемы с хранилищем данных SQL, создайте запрос в службу поддержки, чтобы Группа поддержки разработчиков могла вам помочь.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки
1. Откройте [портал Azure](https://portal.azure.com/).
2. На начальном экране выберите вкладку **Справка и поддержка**.
   
    ![Справка + поддержка](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. В колонке "Справка и поддержка" щелкните **Новый запрос на поддержку** и заполните колонку **Основные сведения**.

   Выберите свой [план поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * Поддержка **устранения неисправностей** предоставляется в рамках поддержки [разработчиков](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)или [Premier](https://azure.microsoft.com/support/plans/premier/) . Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Службы обучения и **консультационные услуги** для **разработчиков** доступны на уровнях поддержки [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) и [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Если у вас есть план поддержки Premier, вы также можете сообщить о проблемах, связанных с хранилищем данных SQL, на [портале Microsoft Premier Online](https://premier.microsoft.com/). Дополнительные сведения о различных планах поддержки, включая область, время отклика, цены и т. д., см. в статье [планы поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Часто задаваемые вопросы о поддержке Azure см. в статье [часто задаваемые](https://azure.microsoft.com/support/faq/)вопросы о поддержке Azure.  
        
     ![Колонка "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![колонка1 "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Заполните колонку **Проблема**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > По умолчанию для каждого экземпляра SQL Server (например, myserver.database.windows.net) предусмотрена следующая **квота на DTU**: 45 000. Эта квота является просто ограничением для безопасности. Вы можете увеличить квоту, отправив запрос в службу поддержки и указав *Квота* в качестве типа запроса. Чтобы вычислить потребности в DTU, умножьте 7,5 на общее [DWU](sql-data-warehouse-overview-what-is.md) . Например, вы хотите разместить две базы данных DW6000 на одном сервере SQL Server. Следует запросить квоту DTU 90 000.  Текущее потребление DTU можно просмотреть в колонке SQL Server на портале. В рамках квоты DTU учитываются как приостановленные, так и активные базы данных. 
   > 
   > 
   
5. Введите **контактные данные**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Щелкните **Создать** , чтобы отправить запрос в службу поддержки.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки
После отправки запроса на поддержку группа поддержки Azure свяжется с вами. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Другие ресурсы
Вы также можете подключиться к сообществу хранилища данных SQL на [Stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) или на [форуме MSDN по хранилищу данных SQL Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 

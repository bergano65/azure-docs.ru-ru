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
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692723"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Создание запроса в службу поддержки для хранилища данных SQL
Если у вас возникли проблемы с хранилищем данных SQL, создайте запрос в службу поддержки, чтобы получить помощь от нашей службы поддержки разработчиков.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки
1. Откройте [портал Azure][Azure portal].
2. На начальном экране выберите вкладку **Справка и поддержка**.
   
    ![Справка + поддержка](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. В колонке "Справка и поддержка" щелкните **Новый запрос на поддержку** и заполните колонку **Основные сведения**.

   Выберите свой [план поддержки Azure][Azure support plan].
   
   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * Поддержка **устранения неисправностей** предоставляется в рамках поддержки [разработчиков][Developer], [Standard][Standard], [Professional Direct][Professional Direct]или [Premier][Premier] . Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Службы обучения и **консультационные услуги** для **разработчиков** доступны на уровнях поддержки [Professional Direct][Professional Direct] и [Premier][Premier] . 
     
     Если у вас есть план поддержки Premier, вы также можете сообщить о проблемах, связанных с хранилищем данных SQL, на [портале Microsoft Premier Online][Microsoft Premier online portal].  Дополнительные сведения о различных планах поддержки, включая область, время отклика, цены и т. д., см. в статье [планы поддержки Azure][Azure support plan] .  Часто задаваемые вопросы о поддержке Azure см. в статье [часто задаваемые][Azure support FAQs]вопросы о поддержке Azure.  
        
     ![Колонка "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![колонка1 "Основные сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Заполните колонку **Проблема**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > По умолчанию для каждого экземпляра SQL Server (например, myserver.database.windows.net) предусмотрена следующая **квота на DTU**: 45 000. Эта квота является просто ограничением для безопасности. Вы можете увеличить квоту, отправив запрос в службу поддержки и указав *Квота* в качестве типа запроса. Чтобы вычислить потребности в DTU, умножьте 7,5 на общее [DWU][DWU] . Например, вы хотите разместить две базы данных DW6000 на одном сервере SQL Server. Следует запросить квоту DTU 90 000.  Текущее потребление DTU можно просмотреть в колонке SQL Server на портале. В рамках квоты DTU учитываются как приостановленные, так и активные базы данных. 
   > 
   > 
   
5. Введите **контактные данные**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Щелкните **Создать** , чтобы отправить запрос в службу поддержки.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки
После отправки запроса с вами свяжется представитель службы поддержки Azure. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Другие ресурсы:
Кроме того, вы можете подключиться к сообществу хранилища данных SQL на [Stack overflow][Stack Overflow] или на [форуме MSDN по хранилищу данных SQL Azure][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


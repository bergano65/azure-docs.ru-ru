---
title: Как создать запрос в службу поддержки
description: Как создать запрос в службу поддержки в Azure синапсе Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195713"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Как создать запрос в службу поддержки для Azure синапсе Analytics
Если у вас возникли проблемы с Azure синапсе Analytics, создайте запрос в службу поддержки, чтобы Группа поддержки разработчиков могла вам помочь.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки
1. Откройте [портал Azure](https://portal.azure.com/).
1. На начальном экране выберите вкладку **Справка и поддержка**.
   
    ![Справка + поддержка](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Ознакомьтесь с [планом поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * Поддержка **устранения неисправностей** предоставляется в рамках поддержки [разработчиков](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)или [Premier](https://azure.microsoft.com/support/plans/premier/) . Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Службы обучения и **консультационные услуги** для **разработчиков** доступны на уровнях поддержки [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) и [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Если у вас есть план поддержки Premier, вы также можете сообщить о проблемах, связанных с хранилищем данных SQL, на [портале Microsoft Premier Online](https://premier.microsoft.com/). Дополнительные сведения о различных планах поддержки, включая область, время отклика, цены и т. д., см. в статье [планы поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Часто задаваемые вопросы о поддержке Azure см. в статье [часто задаваемые](https://azure.microsoft.com/support/faq/)вопросы о поддержке Azure.
1. На странице **Справка и поддержка** выберите **новый запрос в службу поддержки**. Выберите тип проблемы в раскрывающемся меню. Затем продолжайте заполнять сведения на вкладке " **основы** ". Введите **сводку** по проблеме, а затем выберите **тип проблемы** в меню и нажмите кнопку "Сохранить".

    ![Справка + поддержка](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > По умолчанию для каждого экземпляра SQL Server (например, myserver.database.windows.net) предусмотрена следующая **квота на DTU**: 45 000. Эта квота является просто ограничением для безопасности. Вы можете увеличить квоту, отправив запрос в службу поддержки и указав *Квота* в качестве типа запроса. Чтобы вычислить потребности в DTU, умножьте 7,5 на общее [DWU](sql-data-warehouse-overview-what-is.md) . Например, вы хотите разместить две базы данных DW6000 на одном сервере SQL Server. Следует запросить квоту DTU 90 000.  Текущее потребление DTU можно просмотреть в колонке SQL Server на портале. В рамках квоты DTU учитываются как приостановленные, так и активные базы данных. 
   > 

1. Вы можете увидеть решения, которые помогут решить проблему. Если представленные решения не позволяют устранить проблему, выберите **Далее: подробности**. Отправьте сведения о возникшей ошибке и контактной информации. Выберите **Далее: Проверка и создание**
![сведения](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Просмотрите сведения и щелкните **создать** в нижней части формы, чтобы отправить запрос в службу поддержки.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки
После отправки запроса на поддержку группа поддержки Azure свяжется с вами. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Другие ресурсы
Вы также можете подключиться к сообществу хранилища данных SQL на [Stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) или на [форуме MSDN по хранилищу данных SQL Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 

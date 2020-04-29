---
title: Запрос на увеличение квоты и получение поддержки
description: Как создать запрос на поддержку в портал Azure Azure синапсе Analytics. Квота запросов увеличивается или получает поддержку решения проблемы.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350899"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Запрос на увеличение квоты и получение поддержки для Azure синапсе Analytics

В этой статье описывается, как отправить запрос в службу поддержки в портал Azure Azure синапсе Analytics. Этот процесс позволяет запросить увеличение квоты или отправить запрос на техническую поддержку для группы поддержки инженеров.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки

Выполните следующие действия, чтобы создать новый запрос на поддержку из портал Azure Azure синапсе Analytics.

1. В меню [портал Azure](https://portal.azure.com) выберите пункт **Справка и поддержка**.

   ![Ссылка "Справка и поддержка"](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. В окне **Справка и поддержка**выберите **новый запрос в службу поддержки**.

    ![Создать новый запрос в службу поддержки](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Ознакомьтесь с [планом поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * Поддержка **устранения неисправностей** предоставляется в рамках поддержки [разработчиков](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)или [Premier](https://azure.microsoft.com/support/plans/premier/) . Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Услуги **обучения** и **консультирования разработчиков** доступны на уровнях поддержки [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) и [Premier](https://azure.microsoft.com/support/plans/premier/).

   Если у вас есть план поддержки Premier, вы также можете сообщить о проблемах Azure синапсе Analytics на [портале Microsoft Premier Online](https://premier.microsoft.com/). Дополнительные сведения о различных планах поддержки, включая область, время отклика, цены и т. д., см. в статье [планы поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Часто задаваемые вопросы о поддержке Azure см. в статье [часто задаваемые](https://azure.microsoft.com/support/faq/)вопросы о поддержке Azure.

1. В поле **тип проблемы**выберите соответствующий тип проблемы. Чтобы устранить неполадки, выберите пункт **Техническая**. Для запросов на увеличение квоты выберите **пределы службы и подписки (квоты)**.

   ![Выберите тип проблемы](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > В оставшейся части этой статьи рассматриваются запросы на увеличение квот. Но здесь также можно выбрать **технические** сведения о запросах на поддержку решения проблем. Если вы выбрали пункт **технические**, вам будет предложено ввести сводку, а затем указать тип проблемы, выбрав **выбрать тип проблемы**. Вы можете увидеть решения, которые помогут решить проблему. Если решения не помогли устранить проблему, нажмите кнопку **Далее: подробности** и заполните форму, чтобы отправить запрос в службу поддержки.

1. Для запросов на увеличение квоты выберите **Azure синапсе Analytics** для **типа квоты**. Затем выберите **Далее: solutions >>**.

   ![Выберите тип квоты](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. В окне **сведения** выберите **указать сведения** , чтобы ввести дополнительные сведения.

   ![Ссылка "предоставить сведения"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Типы запросов квоты

Если щелкнуть **указать сведения** , откроется окно **сведения о квоте** , в котором можно добавить дополнительные сведения. В следующих разделах описаны различные запросы квот, доступные для Azure синапсе Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Единицы хранилища данных (Dwu) на сервер

Чтобы запросить увеличение количества DWU на сервер, выполните следующие действия.

1. Выберите тип квоты **единиц хранилища данных (DTU) на сервер** .

1. В списке **ресурс** выберите целевой ресурс.

1. В поле **запросить квоту** введите новое ограничение DWU, которое вы запрашиваете.

   ![Сведения о квоте DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Серверов на подписку

Чтобы запросить увеличение числа серверов на подписку, выполните следующие действия.

1. Выберите тип квоты " **серверов на подписку** ".

1. В списке **Расположение** выберите используемый регион Azure. Квота для каждой подписки в каждом регионе.

1. В поле **Новая Квота** введите запрос на максимальное количество серверов в этом регионе.

   ![Сведения о квоте серверов](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Включение доступа к подписке для региона

Некоторые типы предложений доступны не в каждом регионе. Может появиться следующее сообщение об ошибке:

`This location is not available for subscription`

Если вашей подписке требуется доступ в определенном регионе, используйте параметр **другой запрос квоты** для запроса доступа. В запросе укажите сведения о предложении и номере SKU, которые необходимо включить для региона. Сведения о вариантах предложения и SKU см. на странице [цен на Azure синапсе Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Другие сведения о квоте](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Отправка запроса

Последним шагом является заполнение оставшихся сведений о запросе на поддержку базы данных SQL. Затем выберите **Далее: Проверка и создание>>** и после просмотра сведений о запросе нажмите кнопку **создать** , чтобы отправить запрос.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки

После отправки запроса на поддержку группа поддержки Azure свяжется с вами. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Другие ресурсы

Вы также можете подключиться к сообществу Azure синапсе Analytics на [Stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) или на [форуме MSDN по хранилищу данных SQL Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).


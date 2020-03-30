---
title: Запрос увеличения квот ы и получить поддержку
description: Как создать запрос поддержки на портале Azure для azure Synapse Analytics. Запрос увеличения квоты или получить поддержку разрешения проблем.
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350899"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Увеличение квот запросов и получение поддержки для аналитики Azure Synapse

В этой статье описывается, как отправить билет поддержки на портале Azure для Azure Synapse Analytics. Этот процесс позволяет запросить увеличение квоты или отправить запрос на техническую поддержку для группы инженерной поддержки.

## <a name="create-a-support-ticket"></a>Создание запроса в службу поддержки

Используйте следующие шаги для создания нового запроса на поддержку с портала Azure для Azure Synapse Analytics.

1. В меню [портала Azure](https://portal.azure.com) выберите **поддержку Справки .**

   ![Ссылка на справку и поддержку](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. В **справке и поддержке**выберите **новый запрос поддержки.**

    ![Создание нового запроса на поддержку](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Просмотрите [план поддержки Azure.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * Поддержка по **вопросам, связанным с выставлением счетов, квотами и управлением подпиской**, доступна на всех уровнях.
   * **Поддержка break-fix** обеспечивается через [поддержку Developer,](https://azure.microsoft.com/support/plans/developer/) [Standard,](https://azure.microsoft.com/support/plans/standard/) [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)или [Premier.](https://azure.microsoft.com/support/plans/premier/) Проблемы характера «замена или ремонт» — это возникающие при использовании Azure проблемы, относительно которых имеются обоснованные предположения, что их причиной стала корпорация Майкрософт.
   * Услуги **обучения** и **консультирования разработчиков** доступны на уровнях поддержки [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) и [Premier](https://azure.microsoft.com/support/plans/premier/).

   Если у вас есть план поддержки Premier, вы также можете сообщить о проблемах Azure Synapse Analytics на [интернет-портале Microsoft Premier.](https://premier.microsoft.com/) Ознакомиться с [планами поддержки Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) можно получить информацию о различных планах поддержки, включая область охвата, время отклика, ценообразование и т.д.  Для часто задаваемых [Azure support FAQs](https://azure.microsoft.com/support/faq/)вопросов о поддержке Azure см.

1. Для **типа issue**выберите подходящий тип проблемы. Для устранения проблем с устранением неисправностей выберите **технический.** Для запросов на увеличение квот выберите **ограничения службы и подписки (квоты).**

   ![Выберите тип проблемы](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > В этой оставшейся части этой статьи основное внимание уделяется запросам на увеличение квот. Но вы также можете выбрать **Технический** здесь для запросов поддержки разрешения проблем. При выборе **технического,** вас просят предоставить резюме, а затем определить тип проблемы, выбрав **тип проблемы Select.** Вы можете увидеть решения, которые помогут решить вашу проблему. Если представленные решения не решат вашу проблему, выберите **Next:Details** и заполните форму для отправки билета поддержки.

1. Для запросов на увеличение квот выберите **аналитику Azure Synapse** для **типа квоты.** Затем выберите **Следующий: Решения >>**.

   ![Выберите тип квоты](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. В окне **подробностей** выберите **Предоставить подробную информацию** для ввода дополнительной информации.

   ![Ссылка "Предоставить подробную информацию"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Типы запросов квот

Нажатие **На кнопку Подробная** информация Provide отображает окно **детали квоты,** что позволяет добавлять дополнительную информацию. В следующих разделах описаны различные запросы на квоты, доступные для Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Единицы хранилища данных (DWUs) на сервер

Используйте следующие шаги, чтобы запросить увеличение DWUs на сервер.

1. Выберите **единицы хранилища данных (DTUs) на тип квоты сервера.**

1. В списке **ресурсов** выберите целевой ресурс.

1. В поле **квоты Запрос** введите новый лимит DWU, который вы запрашиваете.

   ![Детали квоты DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Серверы за подписку

Используйте следующие шаги, чтобы запросить увеличение числа серверов на одну подписку.

1. Выберите серверы по типу **квоты подписки.**

1. В списке **местоположений** выберите область Azure для использования. Квота на подписку в каждом регионе.

1. В **поле новых квот** введите запрос на максимальное количество серверов в этом регионе.

   ![Детали квоты серверов](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Включить доступ к подписке в регион

Некоторые типы предложений доступны не в каждом регионе. Вы можете увидеть ошибку, такую как:

`This location is not available for subscription`

Если вашей подписке необходим доступ в определенном регионе, используйте опцию **«Другой запрос квот»,** чтобы запросить доступ. В своем запросе укажите информацию о предложении и sKU, которые вы хотите включить для региона. Чтобы изучить варианты предложения [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/synapse-analytics/)и SKU, см.

![Другие детали квоты](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Отправка запроса

Последним шагом является заполнение оставшихся деталей запроса на поддержку базы данных S'L. Затем выберите **Следующий: Обзор и создайте>>, **и после рассмотрения сведений о запросе нажмите **Создать,** чтобы отправить запрос.

## <a name="monitor-a-support-ticket"></a>Мониторинг запроса в службу поддержки

После отправки запроса на поддержку группа поддержки Azure свяжется с вами. Чтобы проверить состояние запроса и сведения о нем, щелкните **Все запросы на поддержку** на панели мониторинга.

![Проверка состояния](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Другие ресурсы

Вы также можете подключиться к сообществу Azure Synapse Analytics на переполнении [стеков](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) или через [форум MSDN хранилища данных Azure S'L.](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)


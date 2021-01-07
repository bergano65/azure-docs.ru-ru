---
title: Запрос на увеличение квоты
description: На этой странице описывается, как создать запрос в службу поддержки, чтобы увеличить квоты для базы данных SQL Azure и Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 44a37a912c5c7a882d21631b8ce2da2c7ba9c05e
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967707"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Увеличение квот запросов для базы данных SQL Azure и SQL Управляемый экземпляр
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В этой статье объясняется, как запросить увеличение квоты для базы данных SQL Azure и Управляемый экземпляр Azure SQL. В нем также объясняется, как включить доступ к подписке для региона.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Создать новый запрос в службу поддержки

Выполните следующие действия, чтобы создать новый запрос на поддержку из портал Azure для базы данных SQL.

1. В главном меню на [портале Azure](https://portal.azure.com) выберите **Справка и поддержка**.

   ![Ссылка "Справка и поддержка"](./media/quota-increase-request/help-plus-support.png)

1. В разделе **Справка и поддержка** выберите **Новый запрос на получение поддержки**.

    ![Создание нового запроса на поддержку](./media/quota-increase-request/new-support-request.png)

1. В качестве **типа проблемы** выберите **пределы службы и подписки (квоты)**.

   ![Выбор типа проблемы](./media/quota-increase-request/select-quota-issue-type.png)

1. В поле **Подписка** выберите подписку, для которой необходимо увеличить квоту.

   ![Выберите подписку для повышения квоты](./media/quota-increase-request/select-subscription-support-request.png)

1. В поле **тип квоты** выберите один из следующих типов квот:

   - **База данных SQL** для квот эластичных баз данных и пулов.
   - **Управляемый экземпляр базы данных SQL** для управляемых экземпляров.

   Затем щелкните **Next: Решения >>** .

   ![Выберите тип квоты](./media/quota-increase-request/select-quota-type.png)

1. В окне **сведения** выберите **Введите сведения** , чтобы ввести дополнительные сведения.

   ![Ссылка для ввода сведений](./media/quota-increase-request/provide-details-link.png)

При нажатии кнопки **Ввод сведений** отображается окно **сведения о квоте** , в котором можно добавить дополнительные сведения. В следующих разделах описаны различные варианты для **базы данных SQL** и типов квот **управляемый экземпляр базы данных SQL** .

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> Типы квот базы данных SQL

В следующих разделах описаны параметры увеличения квоты для типов квот **базы данных SQL** .

- Единиц транзакций базы данных (DTU) на сервер
- Серверов на подписку
- Доступ к региону серии M
- Доступ к региону

### <a name="database-transaction-units-dtus-per-server"></a>Единиц транзакций базы данных (DTU) на сервер

Чтобы запросить увеличение количества единиц DTU на сервер, выполните следующие действия.

1. Выберите тип квоты **единиц транзакций базы данных (DTU) на сервер** .

1. В списке **Ресурс** выберите целевой ресурс.

1. В поле **Новая Квота** введите новое ограничение DTU, которое вы запрашиваете.

   ![Сведения о квоте DTU](./media/quota-increase-request/quota-details-dtus.png)

Дополнительные сведения см. [в разделе ограничения ресурсов для отдельных баз данных с использованием модели приобретения DTU](resource-limits-dtu-single-databases.md) и [ограничений ресурсов для эластичных пулов с использованием модели приобретения DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Серверов на подписку

Чтобы запросить увеличение количества серверов на подписку, выполните следующие действия.

1. Выберите тип квоты **Серверов на подписку**.

1. В списке **Расположение** выберите целевой регион Azure. Квота для каждой подписки в каждом регионе.

1. В поле **Новая квота** введите запрос максимального числа серверов в этом регионе.

   ![Сведения о квотах серверов](./media/quota-increase-request/quota-details-servers.png)

Дополнительные сведения см. в статье [ограничения ресурсов базы данных SQL и управление ресурсами](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Включение доступа к подписке для региона

Некоторые типы предложений доступны не в каждом регионе. Может появиться сообщение об ошибке следующего вида:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Если вашей подписке требуется доступ в определенном регионе, выберите параметр **доступ к региону** . В запросе укажите сведения о предложении и номере SKU, которые необходимо включить для региона. Сведения о вариантах предложения и SKU см. в разделе [цены на базу данных SQL Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Выберите тип квоты **доступа к региону** .

1. В списке **выберите расположение** выберите используемый регион Azure. Квота для каждой подписки в каждом регионе.

1. Введите **модель покупки** и **ожидаемые сведения о потреблении** .

   ![Доступ к региону запроса](./media/quota-increase-request/quota-request.png)

<!--
### <a id="mseries"></a> Enable M-series access to a region

To enable M-series hardware for a subscription and region, a support request must be opened.

1. Select the **M-series region access** quota type.

1. In the **Select a location** list, select the Azure region to use. The quota is per subscription in each region.


   ![Request M-series region access](./media/quota-increase-request/quota-m-series.png)
-->

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a> Тип квоты SQL Управляемый экземпляр

Для типа квоты **SQL управляемый экземпляр** выполните следующие действия.

1. В списке **регион** выберите целевой регион Azure.

1. Введите новые ограничения, которые вы запрашиваете для **подсети** и **Виртуальное ядро**.

   ![Сведения о квоте SQL Управляемый экземпляр](./media/quota-increase-request/quota-details-managed-instance.png)

Дополнительные сведения см. в статье [Общие сведения об ограничениях ресурсов Azure SQL управляемый экземпляр](../managed-instance/resource-limits.md).

## <a name="submit-your-request"></a>Отправка запроса

Последний шаг — указать остальные сведения в запросе квоты базы данных SQL. Затем щелкните **Next: Просмотр и создание >>** . После просмотра сведений о запросе щелкните **Создать**, чтобы отправить запрос.

## <a name="next-steps"></a>Дальнейшие действия

После отправки запроса он будет проверен. Вы получите ответ согласно информации, указанной в форме.

Дополнительные сведения о других ограничениях Azure см. в статье [Подписка Azure, пределы, квоты и ограничения службы](../../azure-resource-manager/management/azure-subscription-service-limits.md).

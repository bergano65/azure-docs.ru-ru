---
title: Отправка оповещений службы ожидания Службы Азуза с помощью PagerDuty
description: Передача в экземпляр PagerDuty персонализированных уведомлений о событиях работоспособности служб.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654075"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Отправка оповещений службы ожидания Azure с помощью PagerDuty с помощью веб-крючков

В этой статье показано, как с помощью веб-перехватчика настроить в PagerDuty уведомления о работоспособности служб Azure. Используя настраиваемый тип интеграции Microsoft Azure в [PagerDuty](https://www.pagerduty.com/), можно без особых усилий добавить оповещения о работоспособности служб в новые или существующие службы PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Создание URL-адреса интеграции работоспособности служб в PagerDuty
1.  Убедитесь, что у вас есть учетная запись [PagerDuty](https://www.pagerduty.com/), и выполните вход в нее.

1.  Перейдите в раздел **Services** (Службы) в PagerDuty.

    ![Раздел Службы (Службы) в PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Выберите **Add New Service** (Добавить новую службу) или откройте существующую настроенную службу.

1.  В разделе **Integration Settings** (Параметры интеграции) выберите следующее:

    а. **Integration Type** (Тип интеграции): Microsoft Azure.

    b. **Название интеграции**: \<Имя\>

    ![Раздел параметров интеграции в PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Заполните остальные обязательные поля и выберите **Add** (Добавить).

1.  Откройте созданную новую интеграцию, а затем скопируйте и сохраните **URL-адрес интеграции**.

    ![URL-адрес интеграции в PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Создание оповещения с помощью PagerDuty на портале Azure
### <a name="for-a-new-action-group"></a>Для новой группы действий сделайте следующее.
1. Выполните шаги 1–8 из раздела [Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. В списке **Действия** определите следующие сведения.

    а. **Тип действия: ** *Веб-перехватчик*.

    b. **Подробности**: **URL-адрес интеграции** для PagerDuty, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

1. Завершив создание оповещения, нажмите кнопку **Сохранить**.

### <a name="for-an-existing-action-group"></a>Для существующей группы действий сделайте следующее.
1. На [портале Azure](https://portal.azure.com/) выберите **Монитор**.

1. В разделе **Параметры** выберите **Группы действий**.

1. Найдите и выберите группу действий, для которой нужно изменить сведения.

1. В список **Действия** добавьте следующие сведения.

    а. **Тип действия: ** *Веб-перехватчик*.

    b. **Подробности**: **URL-адрес интеграции** для PagerDuty, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

1. Завершив изменение группы действий, нажмите кнопку **Сохранить**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Проверка интеграции с веб-перехватчиком с помощью запроса HTTP POST
1. Создайте полезные данные о работоспособности служб, которые хотите отправить. На примере полезной нагрузки [Webhooks для журналов активности Azure](../azure-monitor/platform/activity-log-alerts-webhook.md)можно найти пример полезной нагрузки webhook webhooks.

1. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Вы должны получить `202 Accepted` с сообщением, содержащим идентификатор события.

1. Откройте [PagerDuty](https://www.pagerduty.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [настроить уведомления webhook для существующих систем управления проблемами.](service-health-alert-webhook-guide.md)
- Просмотрите [схему оповещения о журнале активности webhook.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Узнайте об [уведомлениях службы о работоспособности](../azure-monitor/platform/service-notifications.md).
- Подробнее о [группах действий.](../azure-monitor/platform/action-groups.md)
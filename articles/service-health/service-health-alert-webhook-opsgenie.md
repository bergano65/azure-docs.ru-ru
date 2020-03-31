---
title: Отправка оповещений службы ожидания Azure с помощью веб-крючков
description: Передача в экземпляр OpsGenie персонализированных уведомлений о событиях работоспособности служб.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654143"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Отправка оповещений службы ожидания Azure с помощью веб-крючков

В этой статье показано, как с помощью веб-перехватчика настроить в OpsGenie оповещения о работоспособности служб Azure. Используя интеграцию работоспособности служб Azure в [OpsGenie](https://www.opsgenie.com/), можно перенаправлять в OpsGenie оповещения о работоспособности служб Azure. OpsGenie может определить, каких лиц следует уведомить, на основе расписаний по вызовам, используя электронную почту, текстовые сообщения (SMS), телефонные звонки, push-уведомления iOS и Android и выполняя эскалацию оповещений до их подтверждения или закрытия.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Создание URL-адреса интеграции работоспособности служб в OpsGenie
1.  Убедитесь, что у вас есть учетная запись [OpsGenie](https://www.opsgenie.com/), и выполните вход в нее.

1.  Перейдите в раздел **Integrations** (Интеграции) в OpsGenie.

    ![Раздел интеграций в OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Выберите кнопку интеграции **Azure Service Health** (Работоспособность служб Azure).

    ![Кнопка Работоспособность служб Azure (Работоспособность служб Azure) в OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  Задайте **имя** для оповещения и укажите значение в поле **Assigned to Team** (Назначено команде).

1.  Заполните остальные поля, такие как **Recipients** (Получатели), **Enabled** (Включено) и **Suppress Notifications** (Подавлять уведомления).

1.  Скопируйте и сохраните **URL-адрес интеграции**, который уже должен содержать в конце `apiKey`.

    ![URL-адрес интеграции в OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Выберите **Save Integration**(Сохранить интеграцию).

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Создание оповещения с помощью OpsGenie на портале Azure
### <a name="for-a-new-action-group"></a>Для новой группы действий сделайте следующее.
1. Выполните шаги 1–8 из раздела [Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. В списке **Действия** определите следующие сведения.

    а. **Тип действия: ** *Веб-перехватчик*.

    b. **Подробности**: **URL-адрес интеграции** для OpsGenie, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

1. Завершив создание оповещения, нажмите кнопку **Сохранить**.

### <a name="for-an-existing-action-group"></a>Для существующей группы действий сделайте следующее.
1. На [портале Azure](https://portal.azure.com/) выберите **Монитор**.

1. В разделе **Параметры** выберите **Группы действий**.

1. Найдите и выберите группу действий, для которой нужно изменить сведения.

1. В список **Действия** добавьте следующие сведения.

    а. **Тип действия: ** *Веб-перехватчик*.

    b. **Подробности**: **URL-адрес интеграции** для OpsGenie, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

1. Завершив изменение группы действий, нажмите кнопку **Сохранить**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Проверка интеграции с веб-перехватчиком с помощью запроса HTTP POST
1. Создайте полезные данные о работоспособности служб, которые хотите отправить. На примере полезной нагрузки [Webhooks для журналов активности Azure](../azure-monitor/platform/activity-log-alerts-webhook.md)можно найти пример полезной нагрузки webhook webhooks.

1. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. При его отправке должен быть получен ответ `200 OK` с сообщением о состоянии Successful (Успешно).

1. Откройте [OpsGenie](https://www.opsgenie.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как [настроить уведомления webhook для существующих систем управления проблемами.](service-health-alert-webhook-guide.md)
- Просмотрите [схему оповещения о журнале активности webhook.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Узнайте об [уведомлениях службы о работоспособности](../azure-monitor/platform/service-notifications.md).
- Подробнее о [группах действий.](../azure-monitor/platform/action-groups.md)
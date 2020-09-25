---
title: Уведомления в Службах коммуникации Azure
titleSuffix: An Azure Communication Services concept document
description: Отправка уведомлений пользователям приложений, созданных на основе Служб коммуникации Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5be2ff48ea5510c54c79e76e8bae082bd5085794
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945353"
---
# <a name="communication-services-notifications"></a>Уведомления в Службах коммуникации

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Чат в Службах коммуникации Azure и вызов клиентских библиотек создают канал обмена сообщениями в режиме реального времени, который позволяет эффективно и надежно передавать сообщения в подключенные клиенты. Это позволяет создавать расширенные возможности взаимодействия в режиме реального времени в приложениях без необходимости реализации сложной логики опроса HTTP. Однако в мобильных приложениях этот сигнальный канал остается подключенным только тогда, когда приложение активно на переднем плане. Если вы хотите, чтобы пользователи получали входящие вызовы или сообщения чата, пока приложение находится в фоновом режиме, следует использовать push-уведомления.

Push-уведомления позволяют отправлять данные из приложения на мобильные устройства пользователей. Push-уведомления можно использовать для отображения диалогового окна, воспроизведения звука или отображения входящего вызова. Службы коммуникации Azure обеспечивают интеграцию со службой [Сетка событий Azure](https://docs.microsoft.com/azure/event-grid/overview) и [Центрами уведомлений Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview), которые позволяют добавлять push-уведомления в приложения.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Активация push-уведомлений с помощью службы "Сетка событий Azure"

Службы коммуникации Azure интегрируются со службой [Сетка событий Azure](https://azure.microsoft.com/services/event-grid/), чтобы получать уведомления о событиях в реальном времени в надежном, безопасном режиме с возможностью масштабирования. Эту интеграцию можно использовать для создания службы уведомлений, которая предоставляет пользователям мобильные push-уведомления, создав подписку на службу "Сетка событий", которая активирует [Функцию Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) или веб-перехватчик.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Диаграмма, на которой показано, как Службы коммуникации интегрируются со службой "Сетка событий".":::

Дополнительные сведения об обработке событий в Службах коммуникации Azure см. [здесь](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Доставка push-уведомлений с помощью Центров уведомлений Azure

Центр уведомлений Azure можно подключить к ресурсу Служб коммуникации, чтобы автоматически отправлять push-уведомления на мобильное устройство пользователя при получении входящего вызова. Эти push-уведомления следует использовать для пробуждения приложения из фонового режима и отображения интерфейса пользователя, благодаря которому пользователь может принять или отклонить вызов. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Диаграмма, на которой показано, как Службы коммуникации интегрируются с Центром уведомлений Azure.":::

Службы коммуникации используют Центр уведомлений Azure в качестве сквозной службы для взаимодействия с различными службами push-уведомлений, соответствующих для конкретных платформ, с помощью API-интерфейса [Direct Send](https://docs.microsoft.com/rest/api/notificationhubs/direct-send). Это позволяет повторно использовать имеющиеся ресурсы и конфигурации Центра уведомлений Azure, чтобы обеспечить низкую задержку и надежный вызов уведомлений для приложений.

### <a name="notification-hub-provisioning"></a>Подготовка Центра уведомлений 

Чтобы доставить push-уведомления на клиентские устройства с помощью Центров уведомлений, [создайте Центр уведомлений](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) в той же подписке, что и ресурс Служб коммуникации. Центры уведомлений нужно настроить для служб уведомлений платформы, которые необходимо использовать. Сведения о том, как получать push-уведомления в клиентское приложение из Центров уведомлений, см. в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](https://docs.microsoft.com/azure/notification-hubs/ios-sdk-get-started). Вы также можете выбрать целевую клиентскую платформу из раскрывающегося списка в верхней части страницы.

После настройки Центра уведомлений его можно связать с ресурсом Служб коммуникации, предоставив строку подключения для Центра с помощью клиента Azure Resource Manager или с помощью портала Azure. Строка подключения должна содержать разрешения на отправку. Рекомендуется создать другую политику доступа с разрешениями на отправку только для вашего центра. Дополнительные сведения о политиках безопасности и доступа к Центрам уведомлений см. [здесь](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security).

> Примечание. Чтобы включить Службу push-уведомлений Apple VOIP, необходимо задать имя Центра уведомлений в качестве идентификатора пакета приложения с суффиксом `.voip`. См. статью [Использование APNS VOIP с помощью Центров уведомлений](https://docs.microsoft.com/azure/notification-hubs/voip-apns).

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Настройка Центра уведомлений с помощью клиента Azure Resource Manager

Чтобы войти в Azure Resource Manager, выполните следующую процедуру и войдите с использованием своих учетных данных.

```console
armclient login
```

 После успешного входа выполните следующую команду для подготовки Центра уведомлений:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Настройка Центра уведомлений с помощью портала Azure

На портале перейдите к ресурсу Служб коммуникации Azure. В ресурсе Служб коммуникации выберите "Push-уведомления" в меню слева на странице Служб коммуникации и подключитесь к Центру уведомлений, подготовленному ранее. Вам потребуется указать строку подключения и идентификатор ресурса:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Снимок экрана: параметры "Push-уведомления" на портале Azure.":::

#### <a name="device-registration"></a>Регистрация устройства 

Сведения о регистрации маркера устройств в Службах коммуникации см. в [кратком руководстве по голосовым вызовам](../quickstarts/voice-video-calling/getting-started-with-calling.md).

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](https://docs.microsoft.com/azure/event-grid/overview).
* Дополнительные сведения о концепциях Центра уведомлений Azure см. в статье [Документация по Центрам уведомлений Azure](https://docs.microsoft.com/azure/notification-hubs/).

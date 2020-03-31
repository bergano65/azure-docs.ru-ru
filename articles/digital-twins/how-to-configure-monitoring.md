---
title: Как настроить мониторинг - Azure Digital Twins Документы Майкрософт
description: Узнайте, как настроить параметры мониторинга и регистрации для Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511864"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Настройка мониторинга в Azure Digital Twins

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы Azure Monitor, диагностические журналы, журналы активности и другие службы для поддержки сложных потребностей в мониторинге приложения IoT. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

В этой статье перечислены варианты ведения журнала и мониторинга, а также способы их сочетания, характерные для Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Просмотр журналов действий

[Журналы действий](../azure-monitor/platform/platform-logs-overview.md) Azure дают возможность быстрого анализа истории операций и событий на уровне подписки по каждому экземпляру службы Azure.

События уровня подписки включают:

* создание ресурсов;
* удаление ресурсов;
* создание секретов приложений;
* интеграция с другими службами.

Ведение журнала действий по умолчанию включено для Azure Digital Twins. Чтобы получить сведения о нем на портале Azure, выполните указанные ниже действия.

1. Выберите экземпляр Azure Digital Twins.
1. Выберите **Журнал действий**, чтобы открыть эту панель отображения:

    [![Журнал активности](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Расширенные данные о ведении журнала действий вы найдете, выполнив указанные ниже действия.

1. Выберите параметр **Журналы**, чтобы отобразить панель **Обзор аналитики журнала действий**:

    [![Выбор](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. В разделе **Обзор аналитики журнала действий** указаны важнейшие сводные данные по журналу действий.

    [![Обзор аналитики журнала действий]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Используйте **журналы действий** для быстрого анализа событий на уровне подписки.

## <a name="enable-customer-diagnostic-logs"></a>Включение журналов диагностики для клиента

В Azure можно настроить не только ведение журнала действий, но и [параметры диагностики](../azure-monitor/platform/platform-logs-overview.md) для каждого экземпляра Azure. Журналы действий относятся к событиям на уровне подписки, а журналы диагностики дают информацию об истории операций самих ресурсов.

Примеры данных в журнале диагностики:

* время выполнения определяемой пользователем функции;
* код состояния отклика для успешного запроса API;
* получение ключа приложения из хранилища.

Чтобы включить для экземпляра журналы диагностики, сделайте следующее:

1. Откройте нужный ресурс на портале Azure.
1. Выберите **параметры диагностики:**

    [![Параметры диагностики, экран первый](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Выберите **Включить диагностику** для сбора данных (если не включено ранее).
1. Заполните предложенные поля и выберите, как и где будут храниться данные.

    [![Параметры диагностики, экран второй](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Диагностические журналы часто сохраняются с помощью [хранилища файлов Azure](../storage/files/storage-files-deployment-guide.md) и совместно используются [журналами Azure Monitor.](../azure-monitor/log-query/get-started-portal.md) Вы можете выбрать оба варианта.

>[!TIP]
>Используйте **журналы диагностики**, чтобы получить ценные сведения об операциях с ресурсами.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor и Log Analytics

Приложения Интернета вещей собирают в одно целое разрозненные ресурсы, устройства, расположения и данные. Подробное ведение журналов позволяет получать сведения о каждом конкретном элементе, службе и компоненте архитектуры приложений в целом, но часто бывает нужна обобщенная оценка для обслуживания и отладки.

Azure Monitor включает в себя мощную службу анализа журналов, которая позволяет просматривать и анализировать источники журнала в одном месте. Поэтому платформа Azure Monitor удобна для анализа журналов в сложных приложениях Интернета вещей.

Примеры использования:

* запрашивание данных нескольких журналов диагностики;
* просмотр журналов для нескольких определяемых пользователем функций;
* отображение журналов для двух или нескольких служб за определенный промежуток времени.

Полный запрос журнала предоставляется через [журналы Azure Monitor.](../azure-monitor/log-query/log-query-overview.md) Чтобы настроить эти мощные функции:

1. Найдите **Log Analytics** на портале Azure.
1. Будут отображаться доступные экземпляры **рабочего пространства log Analytics.** Выберите один из них и щелкните **Журналы**, чтобы создать запрос.

    [![Служба Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Если у вас еще нет экземпляра **рабочего пространства Log Analytics,** можно создать рабочее пространство, выбрав кнопку **Add:**

    [![Создание OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

После подготовки экземпляра **рабочей области Log Analytics** можно использовать мощные запросы для поиска записей в журналах кратных или поиска с помощью определенных критериев с помощью **управления журналом:**

   [![Управление журналами](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Для получения дополнительной информации о мощных операциях запросов прочитайте [начало работы с запросами.](../azure-monitor/log-query/get-started-queries.md)

> [!NOTE]
> При отправке событий в **рабочее пространство Log Analytics** впервый раз может возникнуть 5-минутная задержка.

Журналы Azure Monitor также предоставляют мощные службы оповещения об ошибках и оповещения, которые можно просмотреть, выбрав **диагностику и решая проблемы:**

   [![Уведомления об ошибках и предупреждениях](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Используйте **рабочее пространство Log Analytics** для запроса историй журналов для нескольких функциональных возможностей приложений, подписок или служб.

## <a name="other-options"></a>Другие варианты

Azure Digital Twins также поддерживает ведение журналов для отдельных приложений и аудит безопасности. Для тщательного обзора всех параметров регистрации Azure, доступных на примере Azure Digital Twins, прочитайте статью [аудита журналов Azure.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [журналах действий](../azure-monitor/platform/platform-logs-overview.md) Azure.

- Глубже изучите параметры системы диагностики Azure в [обзоре журналов диагностики](../azure-monitor/platform/platform-logs-overview.md).

- Узнайте больше о [журналах Azure Monitor](../azure-monitor/log-query/get-started-portal.md).

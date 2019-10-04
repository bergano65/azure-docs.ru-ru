---
title: Настройка мониторинга в Azure Digital Twins | Документация Майкрософт
description: Настройка мониторинга в Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 3fb00977a2e1dba5cf9627b8081aee2f76bc8bd4
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845461"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Настройка мониторинга в Azure Digital Twins

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы Azure Monitor, журналы диагностики, ведение журнала действий и другие службы для поддержки сложных задач мониторинга для приложения IoT. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

В этой статье перечислены варианты ведения журнала и мониторинга, а также способы их сочетания, характерные для Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Просмотр журналов действий

[Журналы действий](../azure-monitor/platform/activity-logs-overview.md) Azure дают возможность быстрого анализа истории операций и событий на уровне подписки по каждому экземпляру службы Azure.

События уровня подписки включают:

* создание ресурсов;
* удаление ресурсов;
* создание секретов приложений;
* интеграция с другими службами.

Ведение журнала действий по умолчанию включено для Azure Digital Twins. Чтобы получить сведения о нем на портале Azure, выполните указанные ниже действия.

1. Выберите экземпляр Azure Digital Twins.
1. Выберите **Журнал действий**, чтобы открыть эту панель отображения:

    [![Журнал действий](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Расширенные данные о ведении журнала действий вы найдете, выполнив указанные ниже действия.

1. Выберите параметр **Журналы**, чтобы отобразить панель **Обзор аналитики журнала действий**:

    [![Выбор](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. В разделе **Обзор аналитики журнала действий** указаны важнейшие сводные данные по журналу действий.

    [![Обзор анализа журнала действий]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Используйте **журналы действий** для быстрого анализа событий на уровне подписки.

## <a name="enable-customer-diagnostic-logs"></a>Включение журналов диагностики для клиента

В Azure можно настроить не только ведение журнала действий, но и [параметры диагностики](../azure-monitor/platform/resource-logs-overview.md) для каждого экземпляра Azure. Журналы действий относятся к событиям на уровне подписки, а журналы диагностики дают информацию об истории операций самих ресурсов.

Примеры данных в журнале диагностики:

* время выполнения определяемой пользователем функции;
* код состояния отклика для успешного запроса API;
* получение ключа приложения из хранилища.

Чтобы включить для экземпляра журналы диагностики, сделайте следующее:

1. Откройте нужный ресурс на портале Azure.
1. Выберите **параметры диагностики**:

    [![Параметры диагностики один](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Выберите **включить диагностику** для получения данных (если они не были включены ранее).
1. Заполните предложенные поля и выберите, как и где будут храниться данные.

    [![Параметры диагностики два](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Журналы диагностики часто сохраняются с помощью [хранилища файлов Azure](../storage/files/storage-files-deployment-guide.md) и совместно используются с [журналами Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Вы можете выбрать оба варианта.

>[!TIP]
>Используйте **журналы диагностики**, чтобы получить ценные сведения об операциях с ресурсами.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor и Log Analytics

Приложения Интернета вещей собирают в одно целое разрозненные ресурсы, устройства, расположения и данные. Подробное ведение журналов позволяет получать сведения о каждом конкретном элементе, службе и компоненте архитектуры приложений в целом, но часто бывает нужна обобщенная оценка для обслуживания и отладки.

Azure Monitor включает в себя мощную службу log Analytics, которая позволяет просматривать и анализировать источники журналов в одном месте. Поэтому платформа Azure Monitor удобна для анализа журналов в сложных приложениях Интернета вещей.

Примеры использования:

* запрашивание данных нескольких журналов диагностики;
* просмотр журналов для нескольких определяемых пользователем функций;
* отображение журналов для двух или нескольких служб за определенный промежуток времени.

Полный запрос журнала предоставляется через [журналы Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Чтобы настроить эти мощные функции:

1. Найдите **Log Analytics** на портале Azure.
1. Вы увидите доступные экземпляры **рабочей области log Analytics** . Выберите один из них и щелкните **Журналы**, чтобы создать запрос.

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Если у вас еще нет экземпляра **рабочей области log Analytics** , можно создать рабочую область, нажав кнопку **Добавить** :

    [![Создание OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

После подготовки экземпляра **рабочей области log Analytics** вы можете использовать мощные запросы для поиска записей в нескольких журналах или поиска с использованием конкретных критериев с помощью **управления журналами**:

   [![Управление журналами](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Дополнительные сведения о мощных операциях с запросами см. в статье [о начале работы с запросами](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> При первой отправке событий в **log Analytics рабочую область** может возникать 5-минутная задержка.

Журналы Azure Monitor также предоставляют мощные службы уведомлений об ошибках и предупреждений, которые можно просмотреть, выбрав пункт **Диагностика и решение проблем**.

   [![Оповещения и уведомления об ошибках](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Используйте **рабочую область log Analytics** для запроса журналов для нескольких функциональных возможностей приложения, подписок или служб.

## <a name="other-options"></a>Другие параметры

Azure Digital Twins также поддерживает ведение журналов для отдельных приложений и аудит безопасности. Подробный обзор всех вариантов ведения журналов Azure, доступных для экземпляра Azure Digital Twins, вы найдете в статье [Аудит журналов Azure](../security/fundamentals/log-audit.md).

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [журналах действий](../azure-monitor/platform/activity-logs-overview.md) Azure.

- Глубже изучите параметры системы диагностики Azure в [обзоре журналов диагностики](../azure-monitor/platform/resource-logs-overview.md).

- Дополнительные сведения см. в статье [журналы Azure Monitor](../azure-monitor/log-query/get-started-portal.md).

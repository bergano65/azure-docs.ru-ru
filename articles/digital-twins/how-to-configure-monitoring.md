---
title: Настройка мониторинга в Azure Digital Twins | Документация Майкрософт
description: Настройка мониторинга в Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9765af13a22a0ae9db8a45c396e7d5a32b71b79c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730325"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Настройка мониторинга в Azure Digital Twins

Azure Digital Twins поддерживает надежные механизмы ведения журнала, мониторинга и аналитики. Разработчики решений могут использовать журналы, журналы диагностики, ведение журнала действий и других служб Azure Monitor для поддержки сложных задач мониторинга приложения Интернета вещей. Можно сочетать несколько вариантов ведения журнала для запрашивания или отображения записей в нескольких службах и получения подробных сведений в журналах для многих служб.

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

    [![Обзор Activity log analytics]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Используйте **журналы действий** для быстрого анализа событий на уровне подписки.

## <a name="enable-customer-diagnostic-logs"></a>Включение журналов диагностики для клиента

В Azure можно настроить не только ведение журнала действий, но и [параметры диагностики](../azure-monitor/platform/diagnostic-logs-overview.md) для каждого экземпляра Azure. Журналы действий относятся к событиям на уровне подписки, а журналы диагностики дают информацию об истории операций самих ресурсов.

Примеры данных в журнале диагностики:

* время выполнения определяемой пользователем функции;
* код состояния отклика для успешного запроса API;
* получение ключа приложения из хранилища.

Чтобы включить для экземпляра журналы диагностики, сделайте следующее:

1. Откройте нужный ресурс на портале Azure.
1. Щелкните **Параметры диагностики**.

    [![Параметры диагностики один](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Щелкните **Включить диагностику**, чтобы начать сбор данных (если не был включен ранее).
1. Заполните предложенные поля и выберите, как и где будут храниться данные.

    [![Параметры диагностики двух](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Часто сохранения журналов диагностики с помощью [хранилища файлов Azure](../storage/files/storage-files-deployment-guide.md) и совместно используется с [журналы Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Вы можете выбрать оба варианта.

>[!TIP]
>Используйте **журналы диагностики**, чтобы получить ценные сведения об операциях с ресурсами.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor и Log Analytics

Приложения Интернета вещей собирают в одно целое разрозненные ресурсы, устройства, расположения и данные. Подробное ведение журналов позволяет получать сведения о каждом конкретном элементе, службе и компоненте архитектуры приложений в целом, но часто бывает нужна обобщенная оценка для обслуживания и отладки.

Azure Monitor включает мощные службу log analytics, что позволяет ведение журнала источников, чтобы просматривать и анализировать в одном месте. Поэтому платформа Azure Monitor удобна для анализа журналов в сложных приложениях Интернета вещей.

Примеры использования:

* запрашивание данных нескольких журналов диагностики;
* просмотр журналов для нескольких определяемых пользователем функций;
* отображение журналов для двух или нескольких служб за определенный промежуток времени.

Запрос полного журнала предоставляется через [журналы Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Чтобы настроить эти мощные функции:

1. Найдите **Log Analytics** на портале Azure.
1. Вы увидите в доступных **рабочей области Log Analytics** экземпляров. Выберите один из них и щелкните **Журналы**, чтобы создать запрос.

    [![Log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Если у вас нет **рабочей области Log Analytics** экземпляр, можно создать рабочую область, щелкнув **добавить** кнопки:

    [![Создать OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Один раз в **рабочей области Log Analytics** экземпляр подготавливается, вы можете использовать мощные запросы на поиск записей в журналах кратных и на основе определенных критериев с помощью **управление журналом**:

   [![Управление журналами](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Дополнительные сведения о мощных операциях с запросами см. в статье [о начале работы с запросами](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Вы возможна задержка 5 минут при отправке событий **рабочей области Log Analytics** в первый раз.

Журналы Azure Monitor также предоставляют мощный ошибки и службы уведомления о предупреждении, которые можно просмотреть, щелкнув **Диагностика и решение проблем**:

   [![Предупреждение и ошибка уведомления](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Используйте **рабочей области Log Analytics** к истории журнала запросов для нескольких функций приложения, подписки и служб.

## <a name="other-options"></a>Другие варианты

Azure Digital Twins также поддерживает ведение журналов для отдельных приложений и аудит безопасности. Подробный обзор всех вариантов ведения журналов Azure, доступных для экземпляра Azure Digital Twins, вы найдете в статье [Аудит журналов Azure](../security/azure-log-audit.md).

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [журналах действий](../azure-monitor/platform/activity-logs-overview.md) Azure.

- Глубже изучите параметры системы диагностики Azure в [обзоре журналов диагностики](../azure-monitor/platform/diagnostic-logs-overview.md).

- Дополнительные сведения о [журналы Azure Monitor](../azure-monitor/log-query/get-started-portal.md).

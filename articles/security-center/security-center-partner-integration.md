---
title: Интеграция решений по обеспечению безопасности в центре безопасности Azure | Документация Майкрософт
description: Сведения о том, как интегрировать центр безопасности Azure с партнерами для повышения общей безопасности ресурсов Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0479918ad2afa99dcd9f18d3ba81f94a3e262e9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479379"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Интеграция решений по обеспечению безопасности в центре безопасности Azure
В этом документе описана процедура управления решениями по обеспечению безопасности, подключенными к центру безопасности Azure, и добавления новых решений.

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Интегрированные решения по обеспечению безопасности Azure
Центр безопасности упрощает включение интегрированных решений по обеспечению безопасности в Azure. Преимущества:

- **Упрощенное развертывание.** Центр безопасности предлагает упрощенную подготовку партнерских решений обеспечения безопасности. For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **Интегрированные обнаружения.** События безопасности автоматически собираются из партнерских решений, объединяются и отображаются в списках оповещений и инцидентов центра безопасности. Эти события используются совместно с результатами сканирования других источников, что дает дополнительные возможности для обнаружения угроз.
- **Единый мониторинг работоспособности и единое управление.** Интеграция событий работоспособности позволяет клиентам отслеживать все партнерские решения в одном расположении. Предоставляются базовые возможности для управления и быстрый доступ к партнерским решениям для расширенной настройки.

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>Как интегрируются решения по обеспечению безопасности
Решения по обеспечению безопасности Azure, развертываемые из центра безопасности, подключены автоматически. You can also connect other security data sources, including computers running on-premises or in other clouds.

![Интеграция решений партнеров](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Управление интегрированными решениями по обеспечению безопасности Azure и другими источниками данных

1. Войдите на [портале Azure](https://azure.microsoft.com/features/azure-portal/).

2. В меню **Microsoft Azure** выберите пункт **Центр безопасности**. Откроется окно **Security Center - Overview** (Центр безопасности — обзор).

3. В меню центра безопасности выберите пункт **Решения безопасности**.

   ![Обзор центра безопасности](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>Подключенные решения

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![Подключенные решения](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Поле состояния партнерского решения может содержать следующее значение:

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* "Проблемы работоспособности" (оранжевый) — решение перестало сообщать о работоспособности.
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> Если данные о работоспособности отсутствуют, центр безопасности отображает дату и время последнего полученного события, позволяя проверить отправку отчетов с устройства. If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **Консоль решения**. Открывает интерфейс управления для этого решения.
   - **Связать виртуальную машину**. Opens the Link Applications page. В ней можно подключить ресурсы к решению партнера.
   - **Удалить решение**.
   - **Настройка**.

   ![Сведения о партнерском решении](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Обнаруженные решения

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> Использование центра безопасности категории "Стандартный" на уровне подписки является обязательным требованием для работы функции обнаружения решений. See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>Добавление источников данных

В разделе **Добавление источников данных** содержатся сведения о других доступных источниках данных, которые можно подключить. Чтобы получить инструкции по добавлению данных из любого из этих источников, щелкните **Добавить**.

![Источники данных](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Экспорт данных в SIEM

> [!NOTE]
> For details of a simpler method (currently in preview) for exporting data to a SIEM, see [Export security alerts and recommendations (Preview)](continuous-export.md). The new method does not use Activity Log as an intermediator and allows direct export from Security Center to Event Hubs (and then on to your SIEM), it also supports the export of Security Recommendations.


You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

В следующих разделах описывается, как настроить потоковую передачу данных в концентратор событий. В инструкциях предполагается, что в вашей подписке Azure настроен центр безопасности Azure.

### <a name="high-level-overview"></a>Общий обзор

![Общий обзор](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Какие данные безопасности Azure передаются в SIEM?

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) В будущих выпусках мы дополним их рекомендациями по безопасности.

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>Создание концентратора событий

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Потоковая передача журнала действий Azure в Центры событий

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Установка соединителя SIEM партнера 

Маршрутизация данных мониторинга в концентратор событий с помощью Azure Monitor обеспечивает интеграцию с партнерским решением SIEM и инструментами мониторинга.

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Пример запроса данных 

Here are some Splunk queries you can use to pull alert data:

| **Описание запроса** | **Запрос** |
|----|----|
| Все оповещения.| index=main Microsoft.Security/locations/alerts|
| Суммирование количества операций по имени.| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Получение сведений об оповещении: время, имя, состояние, идентификатор и подписка. | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как интегрировать партнерские решения в центре безопасности. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md). Ознакомьтесь с ответами на часто задаваемые вопросы об использовании центра безопасности.
* [Блог по безопасности Azure](https://blogs.msdn.com/b/azuresecurity/). Записи блога, посвященные безопасности и соответствию требованиям в Azure.

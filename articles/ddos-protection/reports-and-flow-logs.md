---
title: Стандартные отчеты и журналы потоков для защиты Azure от атак DDoS
description: Узнайте, как настроить отчеты и журналы потоков.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c3bfbdf133777f0bc219d1306f80bd4d38b56ea
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746273"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Настройка журналов потоков и отчетов об устранении рисков атак DDoS 

Защита от атак DDoS Azure (цен. категория "Стандартный") обеспечивает подробные сведения об атаках и визуализацию с помощью аналитики атак DDoS. Клиенты, защищающие виртуальные сети от атак DDoS, могут просматривать подробные сведения о трафике атаки и действиях, предпринятых для устранения атак, благодаря отчетам об устранении рисков атак и журналам потоков устранения рисков. Расширенные телеметрические данные предоставляются через Azure Monitor включая подробные метрики в течение от атак DDoS атаки. Предупреждения можно настроить для любой метрики Azure Monitor, предоставленной с помощью службы "Защита от атак DDoS". Ведение журнала может быть дополнительно интегрировано с [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (концентраторы событий Azure), OMS log Analytics и хранилищем Azure для расширенного анализа через интерфейс диагностики Azure Monitor.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Просмотр и настройка отчетов об устранении уязвимости от атак DDoS
> * Просмотр и Настройка журналов потоков для предотвращения атак от атак DDoS

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
- Прежде чем выполнять действия, описанные в этом руководстве, необходимо создать [план защиты Azure от атак DDoS Standard](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Просмотр и настройка отчетов об устранении уязвимости от атак DDoS

В отчетах об устранении рисков атак используются данные протокола Netflow, которые объединяются для обеспечения подробных сведений об атаке на ваш ресурс. Каждый раз, когда ресурс общедоступного IP-адреса подвергается атаке, начинается устранение рисков и создание отчетов. Каждые 5 минут создается добавочный отчет, кроме того, после устранения рисков создается отчет за весь период устранения. Это гарантирует, что в случае продолжительной атаки DDoS каждые 5 минут можно будет просмотреть наиболее актуальный моментальный снимок отчета об устранении рисков, а после устранения рисков атаки будет доступна полная сводка. 

1. В левой верхней части портала выберите **Все службы**.
2. Введите *Monitor* в поле **Фильтр**. Когда в результатах поиска появится элемент **Monitor**, щелкните его.
3. В разделе **Параметры** выберите **Параметры диагностики**.
4. Выберите **подписку** и **группу ресурсов**, содержащую общедоступный IP-адрес, для которого требуется вести журнал.
5. Для параметра **Тип ресурса** выберите значение **Общедоступный IP-адрес**, затем выберите общедоступный IP-адрес, для метрик которого требуется вести журнал.
6. Выберите **Turn on diagnostics to collect the DDoSMitigationReports log** (Включить диагностику для сбора журнала DDoSMitigationReports) и выберите необходимые параметры из перечисленных ниже.

    - **Archive to a storage account** (Архивация в учетную запись хранения). Запись данных в учетную запись хранения Azure. Дополнительные сведения об этом параметре см. в разделе [архивирование журналов ресурсов](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Stream to an event hub** (Потоковая передача в концентратор событий). Позволяет получателю журнала принимать журналы с помощью концентратора событий Azure. Центры событий обеспечивают интеграцию со Splunk или другими системами SIEM. Дополнительные сведения об этом параметре см. в статье [потоковая передача журналов ресурсов в концентратор событий](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Send to log Analytics**: записывает журналы в службу Azure Monitor. Дополнительные сведения об этом параметре см. [в разделе Получение журналов для использования в журналах Azure Monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Добавочный отчет об устранении рисков и отчет после устранения рисков атаки содержат приведенные ниже поля.
- "Attack vectors" (Направления атаки).
- "Traffic statistics" (Статистика трафика).
- "Reason for dropped packets" (Причина для удаленных пакетов).
- "Protocols involved" (Используемые протоколы).
- "Top 10 source countries or regions" (10 основных исходных стран или регионов).
- "Top 10 source ASNs" (10 основных исходных ASN).

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Просмотр и Настройка журналов потоков для предотвращения атак от атак DDoS
Журналы потоков для предотвращения атак позволяют просматривать удаленный трафик, перенаправленный трафик и другие интересные точки данных во время активной от атак DDoS атаки в ближайшем режиме реального времени. Вы можете передать постоянный поток этих данных в метку Azure или в сторонние системы SIEM с помощью концентратора событий для мониторинга практически в реальном времени. для этого выполните возможные действия и устраните необходимость в операциях защиты.

1. В левой верхней части портала выберите **Все службы**.
2. Введите *Monitor* в поле **Фильтр**. Когда в результатах поиска появится элемент **Monitor**, щелкните его.
3. В разделе **Параметры** выберите **Параметры диагностики**.
4. Выберите **подписку** и **группу ресурсов**, содержащую общедоступный IP-адрес, для которого требуется вести журнал.
5. Для параметра **Тип ресурса** выберите значение **Общедоступный IP-адрес**, затем выберите общедоступный IP-адрес, для метрик которого требуется вести журнал.
6. Выберите **Turn on diagnostics to collect the DDoSMitigationFlowLogs log** (Включить диагностику для сбора журнала DDoSMitigationFlowLogs) и выберите необходимые параметры из перечисленных ниже.

    - **Archive to a storage account** (Архивация в учетную запись хранения). Запись данных в учетную запись хранения Azure. Дополнительные сведения об этом параметре см. в разделе [архивирование журналов ресурсов](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Stream to an event hub** (Потоковая передача в концентратор событий). Позволяет получателю журнала принимать журналы с помощью концентратора событий Azure. Центры событий обеспечивают интеграцию со Splunk или другими системами SIEM. Дополнительные сведения об этом параметре см. в статье [потоковая передача журналов ресурсов в концентратор событий](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Send to log Analytics**: записывает журналы в службу Azure Monitor. Дополнительные сведения об этом параметре см. [в разделе Получение журналов для использования в журналах Azure Monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Этот [шаблон](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) создает определение политики Azure, чтобы включить ведение журнала диагностики.

### <a name="azure-sentinel-data-connector"></a>Соединитель данных Sentinel Azure

Вы можете подключать журналы потоков для предотвращения атак в Azure Sentinel, просматривать и анализировать данные в книгах, создавать пользовательские оповещения и внедрять их в процессы исследования. Чтобы подключиться к Azure Sentinel, см. статью [Подключение к Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Соединитель от атак DDoS Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Книга защиты от атак DDoS Azure

Чтобы просматривать данные журналов потоков на панели мониторинга Аналитики Azure, вы можете импортировать пример панели мониторинга из https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook.

Журналы потоков будут содержать следующие поля: 
- Исходный IP-адрес
- Конечный IP-адрес
- Исходный порт 
- Конечный порт 
- "Protocol type" (Тип протокола). 
- "Action taken during mitigation" (Действие, выполненное при снижении рисков)

![Книга защиты от атак DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

Анализ атак будет работать, только если включен стандарт защиты от атак DDoS в виртуальной сети общедоступного IP-адреса. 

## <a name="sample-log-outputs"></a>Примеры выходных данных журнала

Ниже приведены снимки экрана, например выходные данные журнала:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![Ддосмитигатионфловлогс защита от атак DDoS](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![Ддоспротектионнотификатионс защита от атак DDoS](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![Ддосмитигатионрепортс защита от атак DDoS](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

- Просмотр и настройка отчетов об устранении уязвимости от атак DDoS
- Просмотр и Настройка журналов потоков для предотвращения атак от атак DDoS

Чтобы узнать, как тестировать и имитировать атаку от атак DDoS, см. Руководство по тестированию моделирования.

> [!div class="nextstepaction"]
> [Тестирование через моделирования](test-through-simulations.md)
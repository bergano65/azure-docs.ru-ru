---
title: Часто задаваемые вопросы (часто задаваемые вопросы) о наблюдательном средств сети Azure Документы Майкрософт
description: В этой статье часто возникают ответы на часто задаваемые вопросы об службе наблюдения за сетью Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471862"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Часто задаваемые вопросы (часто задаваемые вопросы) о наблюдательном средстве сети Azure
Служба [наблюдения за сетью Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) предоставляет набор инструментов для мониторинга, диагностики, просмотра метрик и включения или отработки журналов в виртуальной сети Azure. В этой статье ответы на общие вопросы об услуге.

## <a name="general"></a>Общие сведения

### <a name="what-is-network-watcher"></a>Что такое Наблюдатель за сетями?
Network Watcher предназначен для мониторинга и ремонта сетевого состояния компонентов IaaS (Infrastructure-as-a-Service), который включает в себя виртуальные машины, виртуальные сети, шлюзы приложений, балансеры нагрузки и другие ресурсы в виртуальной сети Azure. Это не решение для мониторинга инфраструктуры PaaS (Platform-as-a-Service) или получения веб-аналитики/мобильной аналитики.

### <a name="what-tools-does-network-watcher-provide"></a>Какие инструменты предоставляет Network Watcher?
Network Watcher предоставляет три основных набора возможностей
* Наблюдение
  * [Представление Topology](https://docs.microsoft.com/azure/network-watcher/view-network-topology) показывает ресурсы в виртуальной сети и отношения между ними.
  * [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/connection-monitor) позволяет отслеживать подключение и задержку между VM и другим сетевым ресурсом.
  * [Монитор производительности сети](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) позволяет отслеживать подключение и просеченность в гибридных сетевых архитектурах, схемах Expressroute и конечных точках обслуживания/приложения.  
* Диагностика
  * [IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) позволяет обнаруживать проблемы фильтрации трафика на уровне VM.
  * [Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) поможет вам проверить маршруты трафика и обнаружить проблемы маршрутов.
  * [Connection Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) позволяет проводить одноразовую проверку подключения и задержки между VM и другим сетевым ресурсом.
  * [Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) позволяет захватить весь трафик на VM в виртуальной сети.
  * [VPN Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) проводит несколько диагностических проверок на ваших VPN шлюзах и соединениях, чтобы помочь отладить проблемы.
* Ведение журнала
  * [NsG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) позволяет регистрировать весь трафик в [группах сетевой безопасности (НСГ)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) обрабатывает данные журнала NSG Flow Log, что позволяет визуализировать, запросить, проанализировать и понять свой сетевой трафик.


Для получения более подробной информации смотрите [страницу обзора сетевого наблюдателя](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Как работает ценообразование сетевого наблюдателя?
Посетите [страницу ценообразования](https://azure.microsoft.com/pricing/details/network-watcher/) для компонентов Network Watcher и их цен.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>В каких регионах поддерживается/доступна сеть Watcher?
Вы можете просмотреть последнюю региональную доступность на [странице доступности службы Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Какие разрешения необходимы для использования Network Watcher?
Ознакомьтесь со списком [разрешений RBAC, необходимых для использования Network Watcher.](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions) Для развертывания ресурсов требуется разрешение участников NetworkWatcherRG (см. ниже).

### <a name="how-do-i-enable-network-watcher"></a>Как включить Наблюдатель за сетями?
Служба сетевого наблюдения [включается автоматически](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) для каждой подписки.

### <a name="what-is-the-network-watcher-deployment-model"></a>Что такое модель развертывания сетевого наблюдения?
Родительский ресурс Network Watcher развертывается с уникальным экземпляром в каждом регионе. Формат имен: NetworkWatcher_RegionName. Пример: NetworkWatcher_centralus является ресурсом Network Watcher для региона «Центральный США».

### <a name="what-is-the-networkwatcherrg"></a>Что такое NetworkWatcherRG?
Ресурсы Network Watcher находятся в скрытой группе ресурсов **NetworkWatcherRG,** которая создается автоматически. Например, ресурс NSG Flow Logs является детским ресурсом Network Watcher и включен в NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Почему мне нужно установить расширение Network Watcher? 
Расширение Network Watcher требуется для любой функции, которая должна генерировать или перехватывать трафик с VM. 

### <a name="which-features-require-the-network-watcher-extension"></a>Какие функции требуют расширения Network Watcher?
Функции Packet Capture, Connection Troubleshoot и Connection Monitor нуждаются в расширении Network Watcher.

### <a name="what-are-resource-limits-on-network-watcher"></a>Каковы ограничения ресурсов на Network Watcher?
Просмотрите страницу [ограничений службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) для всех ограничений.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Почему в каждом регионе разрешен только один экземпляр сетевого наблюдения? 
Network Watcher просто должен быть включен один раз для подписки на его функции для работы, это не предел обслуживания.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Как управлять ресурсом сетевого наблюдения? 
Ресурс Network Watcher представляет службу бэкэнда для Network Watcher и полностью управляется Azure. Клиентам не нужно управлять им. Операции, такие как перемещение, не поддерживаются на ресурсе. Тем не менее, [ресурс может быть удален.](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal) 

## <a name="nsg-flow-logs"></a>NsG Поток журналы

### <a name="what-does-nsg-flow-logs-do"></a>Что делает журналы потока NSG?
Сетевые ресурсы Azure можно комбинировать и управлять через [группы сетевой безопасности (НСГ).](https://docs.microsoft.com/azure/virtual-network/security-overview) NsG Flow Logs позволяет регистрировать информацию о потоке 5-тупл о всех тёбо через свои НСУ. Необработанные журналы потока записываются в учетную запись хранилища Azure, откуда они могут быть дополнительно обработаны, проанализированы, запрошены или экспортированы по мере необходимости.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Как использовать журналы NSG Flow Logs с учетной записью хранения за брандмауэром?

Чтобы использовать учетную запись хранилища за брандмауэром, необходимо предоставить исключение для надежных служб Майкрософт для доступа к учетной записи хранилища:

* Перейдите к учетной записи хранилища, введя имя учетной записи хранилища в глобальном поиске на портале или со [страницы Учетных записей хранения](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* В разделе **параметров** выберите **Брандмауэры и виртуальные сети**.
* В "Разрешить доступ от" выберите **Выбранные сети.** Затем под **исключениями**отметьте поле рядом с **"Разрешить доверенным службам Майкрософт для доступа к этой учетной записи хранения"** 
* Если они уже выбраны, никаких изменений не требуется.  
* Найдите свою целевую NSG на [странице обзора журналов потоков NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) и включите журналы потока NSG с выбранной выше учетной записью хранения.

Через несколько минут можно проверить журналы хранилища. Вы должны увидеть обновленную метку времени или новый файл JSON.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Как использовать журналы NSG Flow Logs с учетной записью хранилища за конечную точку службы?

Логи потока NSG совместимы с конечными точками обслуживания, не требуя дополнительной конфигурации. Пожалуйста, смотрите [учебник по включению конечных точек обслуживания](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) в вашей виртуальной сети.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>В чем разница между версиями журналов потока 1 & 2?
Версия 2 Flow Logs вводит понятие *Flow State* & хранит информацию о передаваемых байтах и пакетах. [Читать далее](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Next Steps
 - Зайди на нашу [страницу обзора документации](https://docs.microsoft.com/azure/network-watcher/) для некоторых учебников, чтобы вы начали с Network Watcher.

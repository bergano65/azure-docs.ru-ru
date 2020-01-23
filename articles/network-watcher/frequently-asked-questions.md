---
title: Часто задаваемые вопросы о наблюдателе за сетями Azure | Документация Майкрософт
description: В этой статье содержатся ответы на часто задаваемые вопросы о службе наблюдателя за сетями Azure.
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
ms.openlocfilehash: 856c249b72e9e0ff8667d10821ad14b3432b0775
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509195"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Часто задаваемые вопросы о наблюдателе за сетями Azure
Служба [наблюдателя за сетями Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) предоставляет набор средств для мониторинга, диагностики, просмотра метрик и включения или отключения журналов для ресурсов в виртуальной сети Azure. В этой статье содержатся ответы на часто задаваемые вопросы о службе.

## <a name="general"></a>Общие

### <a name="what-is-network-watcher"></a>Что такое Наблюдатель за сетями?
Наблюдатель за сетями предназначен для отслеживания и восстановления работоспособности сети компонентов IaaS (инфраструктура как услуга), включая виртуальные машины, виртуальные сети, шлюзы приложений, подсистемы балансировки нагрузки и другие ресурсы в виртуальной сети Azure. Это решение не является решением для мониторинга инфраструктуры PaaS (платформа как услуга) или получения веб-или мобильной аналитики.

### <a name="what-tools-does-network-watcher-provide"></a>Какие средства предоставляет наблюдатель за сетями?
Наблюдатель за сетями предоставляет три основных набора возможностей
* Мониторинг
  * [Представление топологии](https://docs.microsoft.com/azure/network-watcher/view-network-topology) показывает ресурсы в виртуальной сети и связи между ними.
  * [Монитор подключений](https://docs.microsoft.com/azure/network-watcher/connection-monitor) позволяет отслеживать подключение и задержку между виртуальной машиной и другим сетевым ресурсом.
  * [Монитор производительности сети](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) позволяет отслеживать подключение и задержки между архитектурой гибридных сетей, каналами Expressroute и конечными точками службы или приложения.  
* Диагностика:
  * [Проверка IP-потока](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) позволяет обнаруживать проблемы фильтрации трафика на уровне виртуальной машины.
  * [Следующий прыжок](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) помогает проверять маршруты трафика и обнаруживать проблемы маршрутизации.
  * [Устранение неполадок подключения](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) позволяет выполнять одноразовую проверку подключения и задержку между виртуальной машиной и другим сетевым ресурсом.
  * [Запись пакетов](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) позволяет захватить весь трафик на виртуальной машине в виртуальной сети.
  * [Устранение неполадок VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) запускает несколько проверок диагностики на VPN-шлюзах и подключениях, чтобы помочь в отладке проблем.
* Ведение журнала
  * [Журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) позволяют регистрировать весь трафик в [группах безопасности сети (группы безопасности сети)](https://docs.microsoft.com/azure/virtual-network/security-overview) .
  * [Аналитика трафика](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) обрабатывает данные журнала потоков NSG, позволяя визуализировать, запрашивать, анализировать и понимать сетевой трафик.


Более подробные сведения см. на [странице обзора службы "наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)".


### <a name="how-does-network-watcher-pricing-work"></a>Как работают цены наблюдателя за сетями?
Посетите [страницу с ценами](https://azure.microsoft.com/pricing/details/network-watcher/) на компоненты наблюдателя за сетями и их цены.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Какие регионы поддерживаются или доступны в наблюдателе за сетями?
Последнюю версию региональной доступности можно просмотреть на [странице доступности службы Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) .

### <a name="what-are-resource-limits-on-network-watcher"></a>Что такое ограничения ресурсов для наблюдателя за сетями?
Все ограничения см. на странице " [ограничения службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) ".  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Почему для региона разрешен только один экземпляр наблюдателя за сетями?
Наблюдатель за сетями должен быть включен один раз для подписки, чтобы ее функции работали, это не ограничение службы.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Зачем нужно устанавливать расширение наблюдателя за сетями? 
Расширение наблюдателя за сетями требуется для любой функции, требующей создания или перехвата трафика от виртуальной машины. 

### <a name="which-features-require-the-network-watcher-extension"></a>Какие функции требуются для расширения наблюдателя за сетями?
Для записи пакетов, устранения неполадок подключения и монитора подключения требуется наличие расширения наблюдателя за сетями.

## <a name="nsg-flow-logs"></a>Журналы потоков NSG

### <a name="what-does-nsg-flow-logs-do"></a>Что делают журналы потоков NSG?
Сетевые ресурсы Azure можно объединять и управлять ими с помощью [групп безопасности сети (группы безопасности сети)](https://docs.microsoft.com/azure/virtual-network/security-overview). Журналы потоков NSG позволяют записывать в журнал сведения о потоке с пятью кортежами по всему трафику через группы безопасности сети. Журналы необработанных потоков записываются в учетную запись хранения Azure, откуда они могут быть обработаны, проанализированы, запрошены или экспортированы по мере необходимости.

### <a name="how-do-i-use-nsg-flow-logs-on-a-storage-account-with-a-firewall"></a>Разделы справки использовать журналы потоков NSG в учетной записи хранения с брандмауэром?

Чтобы использовать учетную запись хранения с брандмауэром, необходимо предоставить исключение для доступа к учетной записи хранения в доверенных службах Майкрософт:

* Перейдите к учетной записи хранения, введя имя учетной записи хранения в глобальный поиск на портале или на [странице учетные записи хранения](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) .
* В разделе **параметров** выберите **Брандмауэры и виртуальные сети**.
* В окне "разрешить доступ из" выберите **Выбранные сети**. Затем в разделе **исключения**установите флажок **"разрешить доверенным службам Майкрософт доступ к этой учетной записи хранения"** . 
* Если они уже выбраны, никаких изменений не требуется.  
* Выберите целевую NSG на [странице Обзор журналов потоков NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) и включите журналы последовательностей NSG с выбранной учетной записью хранения.

Через несколько минут можно проверить журналы хранилища. Вы должны увидеть обновленную метку времени или новый файл JSON.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints-for-storage"></a>Разделы справки использовать журналы потоков NSG с конечными точками службы для хранения?

Ознакомьтесь с [руководством по включению конечных точек службы](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint). 


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>В чем разница между журналами потоков версии 1 & 2?
Журналы потоков версии 2 представляют концепцию *состояния потока* & хранят сведения о передаваемых байтах и пакетах. [Дополнительные сведения](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Следующие шаги
 - Перейдите к нашей [странице с обзором документации](https://docs.microsoft.com/azure/network-watcher/) по некоторым руководствам, чтобы приступить к работе с наблюдателем за сетями.

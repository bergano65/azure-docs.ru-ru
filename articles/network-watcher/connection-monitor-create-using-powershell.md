---
title: Создание монитора подключения с использованием PowerShell
titleSuffix: Azure Network Watcher
description: Узнайте, как создать монитор подключений с помощью PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: fa8b2d967a336343d23c5f6aa4477ebcf2396407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949043"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Создание монитора подключения с помощью PowerShell

Узнайте, как создать монитор подключения для отслеживания взаимодействия между ресурсами с помощью PowerShell.

## <a name="before-you-begin"></a>Подготовка к работе 

В мониторах подключений, создаваемых в мониторе подключения, можно добавить локальные компьютеры и виртуальные машины Azure в качестве источников. Эти мониторы соединений также могут отслеживать подключение к конечным точкам. Конечные точки могут находиться в Azure или на любом другом URL-адресе или IP.

Монитор подключения включает следующие сущности:

* **Ресурс монитора подключения** — ресурс Azure определенного региона. Все следующие сущности являются свойствами ресурса монитора подключения.
* **Конечная точка** — источник или назначение, участвующие в проверках подключения. Примеры конечных точек: виртуальные машины Azure, локальные агенты, URL-адреса и IP-адрес.
* **Конфигурация теста** — конфигурация, зависящая от протокола, для теста. В зависимости от выбранного протокола можно определить порт, пороговые значения, частоту проверки и другие параметры.
* **Тестовая группа** — группа, содержащая конечные точки источника, конечные точки назначения и конфигурации тестов. Монитор подключения может содержать более одной тестовой группы.
* **Тест** — сочетание исходной конечной точки, конечной точки назначения и конфигурации теста. Тест — это наиболее детализированный уровень доступности данных мониторинга. Данные мониторинга включают в себя процент проверок, завершившихся сбоем, и время приема-передачи (RTT).

    ![Схема, показывающая монитор подключения, определение связи между группами тестов и тестами](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Действия по созданию с помощью PowerShell

Используйте следующие команды для создания монитора подключения с помощью PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Описание свойств

* connectionMonitorName — имя ресурса монитора подключения.

* Идентификатор подписки, для которой требуется создать монитор подключения

* NW — идентификатор ресурса наблюдателя за сетями, в котором будет создан CM 

* Location — регион, в котором будет создан монитор подключения

* Конечные точки
    * имя — уникальное имя для каждой конечной точки.
    * resourceId — для конечных точек Azure идентификатор ресурса ссылается на Azure Resource Manager идентификатор ресурса для виртуальных машин. Для конечных точек, не относящихся к Azure, идентификатор ресурса ссылается на идентификатор ресурса Azure Resource Manager для Log Analytics рабочей области, связанной с агентами, отличными от агентов Azure.
    * адрес — применяется, только если не указан идентификатор ресурса или идентификатор ресурса Log Analytics рабочей области. Если используется с Log Analyticsным ИДЕНТИФИКАТОРом ресурса, это означает полное доменное имя агента, который можно использовать для мониторинга. Если используется без идентификатора ресурса, это может быть URL-адрес или IP-адреса любой общедоступной конечной точки.
    * фильтр. для конечных точек, не относящихся к Azure, используйте фильтр для выбора агентов из рабочей области Log Analytics, которая будет использоваться для мониторинга в ресурсе монитора подключения. Если фильтры не заданы, для мониторинга можно использовать все агенты, принадлежащие рабочей области Log Analytics.
        * Тип — задайте для параметра Тип значение "адрес агента".
        * адрес — укажите адрес в качестве полного доменного имени локального агента.

* Тестовые группы
    * Имя — имя группы тестов.
    * Тестконфигуратионс — конфигурации тестирования в зависимости от того, какие конечные точки источника подключаются к конечным точкам
    * источники — выберите конечные точки, созданные выше. Для конечных точек источника на основе Azure должно быть установлено расширение наблюдателя за сетями Azure, а в других конечных точках источника, созданных на основе Azure, требуется Хавеазуре Log Analytics Agent. Сведения об установке агента для источника см. в разделе [Install Monitoring](./connection-monitor-overview.md#install-monitoring-agents)Agents.
    * назначения — выберите созданные ранее конечные точки. Вы можете наблюдать за подключением к виртуальным машинам Azure или любой конечной точке (общедоступным IP-АДРЕСом или полным доменным именем), указав их в качестве назначений. В одной тестовой группе можно добавить виртуальные машины Azure, URL-адреса Office 365, URL-адреса Dynamics 365 и пользовательские конечные точки.
    * отключить — используйте это поле, чтобы отключить наблюдение для всех источников и назначений, которые указывает группа тестирования.

* Конфигурации тестов
    * Name — имя конфигурации теста.
    * Тестфрекуенцисек — укажите, как часто источники будут проверять связь с адресатами по указанному протоколу и порту. Можно выбрать 30 секунд, 1 минуту, 5 минут, 15 минут или 30 минут. Источники проверяют возможность подключения к местам назначения на основе выбранного значения. Например, если выбрать 30 секунд, источники будут проверять подключение к назначению по крайней мере один раз в течение 30 секунд.
    * Протокол — можно выбрать TCP, ICMP, HTTP или HTTPS. В зависимости от протокола можно использовать определенные конфигурации протокола.
        * Преферхттпс — укажите, следует ли использовать HTTPS через HTTP.
        * Порт — укажите выбранный порт назначения.
        * Дисаблетрацерауте — применяется к группам тестов с протоколом TCP или ICMP. ИТ-отделы не обнаруживают возможности обнаружения топологии и приема-передачи по прыжкам.
    * Сукцесссрешолд — можно задать пороговые значения для следующих сетевых параметров:
        * Чекксфаиледперцент — задайте процент проверок, которые могут завершаться ошибкой, когда источники проверяют подключение к местам назначения, используя указанные критерии. Для протокола TCP или ICMP процент неудачных проверок может равняться проценту потери пакетов. Для протокола HTTP это поле представляет процент HTTP-запросов, которые не получили ответа.
        * Раундтриптимемс — задайте время приема-передачи в миллисекундах, в течение которого источники могут быть выбраны для подключения к назначению в конфигурации теста.

## <a name="scale-limits"></a> Ограничения масштабирования

Мониторы подключений имеют следующие ограничения масштабирования:

* Максимальное число мониторов подключения на подписку в одном регионе: 100
* Максимальное число групп тестов на монитор каждого подключения: 20
* Максимальное число источников и назначений на монитор подключения: 100
* Максимальное число конфигураций тестов на монитор каждого подключения: 20

## <a name="next-steps"></a>Следующие шаги

* Узнайте, [как анализировать данные мониторинга и настраивать оповещения](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts) .
* Узнайте [, как диагностировать проблемы в сети](./connection-monitor-overview.md#diagnose-issues-in-your-network)
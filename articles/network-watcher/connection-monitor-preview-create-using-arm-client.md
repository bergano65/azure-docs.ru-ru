---
title: Создание монитора подключения — ARMClient
titleSuffix: Azure Network Watcher
description: Узнайте, как создать монитор подключений (Предварительная версия) с помощью ARMClient.
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
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567998"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Создание монитора подключения (Предварительная версия) с помощью ARMClient

Узнайте, как создать монитор подключений (Предварительная версия) для отслеживания взаимодействия между ресурсами с помощью ARMClient. Он поддерживает Гибридные развертывания в облаке Azure.

## <a name="before-you-begin"></a>Перед началом 

В мониторах подключений, создаваемых в мониторе подключений (Предварительная версия), можно добавить локальные компьютеры и виртуальные машины Azure в качестве источников. Эти мониторы соединений также могут отслеживать подключение к конечным точкам. Конечные точки могут находиться в Azure или на любом другом URL-адресе или IP.

Монитор подключения (Предварительная версия) включает следующие сущности:

* **Ресурс монитора подключения** — ресурс Azure определенного региона. Все следующие сущности являются свойствами ресурса монитора подключения.
* **Конечная точка** — источник или назначение, участвующие в проверках подключения. Примеры конечных точек: виртуальные машины Azure, локальные агенты, URL-адреса и IP-адрес.
* **Конфигурация теста** — конфигурация, зависящая от протокола, для теста. В зависимости от выбранного протокола можно определить порт, пороговые значения, частоту проверки и другие параметры.
* **Тестовая группа** — группа, содержащая конечные точки источника, конечные точки назначения и конфигурации тестов. Монитор подключения может содержать более одной тестовой группы.
* **Тест** — сочетание исходной конечной точки, конечной точки назначения и конфигурации теста. Тест — это наиболее детализированный уровень доступности данных мониторинга. Данные мониторинга включают в себя процент проверок, завершившихся сбоем, и время приема-передачи (RTT).

    ![Схема, показывающая монитор подключения, определение связи между группами тестов и тестами](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Шаги для создания образца шаблона ARM

Используйте следующий код, чтобы создать монитор подключения с помощью ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Ниже приведена команда развертывания.
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Описание свойств

* connectionMonitorName — имя ресурса монитора подключения.

* Идентификатор подписки, для которой требуется создать монитор подключения

* NW — идентификатор ресурса наблюдателя за сетями, в котором будет создан CM 

* Location — регион, в котором будет создан монитор подключения

* Конечные точки
    * имя — уникальное имя для каждой конечной точки.
    * resourceId — для конечных точек Azure идентификатор ресурса относится к ИДЕНТИФИКАТОРу ресурса Azure Resource Manager (ARM) для виртуальных машин. Для конечных точек, не относящихся к Azure, идентификатор ресурса ссылается на идентификатор ресурса ARM для Log Analytics рабочей области, связанной с агентами, отличными от агентов Azure.
    * адрес — применяется, только если не указан идентификатор ресурса или идентификатор ресурса Log Analytics рабочей области. Если используется с Log Analyticsным ИДЕНТИФИКАТОРом ресурса, это означает полное доменное имя агента, который можно использовать для мониторинга. Если используется без идентификатора ресурса, это может быть URL-адрес или IP-адреса любой общедоступной конечной точки.
    * фильтр. для конечных точек, не относящихся к Azure, используйте фильтр для выбора агентов из рабочей области Log Analytics, которая будет использоваться для мониторинга в ресурсе монитора подключения. Если фильтры не заданы, для мониторинга можно использовать все агенты, принадлежащие рабочей области Log Analytics.
        * Тип — задайте для параметра Тип значение "адрес агента".
        * адрес — укажите адрес в качестве полного доменного имени локального агента.

* Тестовые группы
    * Имя — имя группы тестов.
    * Тестконфигуратионс — конфигурации тестирования в зависимости от того, какие конечные точки источника подключаются к конечным точкам
    * источники — выберите конечные точки, созданные выше. Для конечных точек источника на основе Azure должно быть установлено расширение наблюдателя за сетями Azure, а в других конечных точках источника, созданных на основе Azure, требуется Хавеазуре Log Analytics Agent. Сведения об установке агента для источника см. в разделе [Install Monitoring](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)Agents.
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

## <a name="scale-limits"></a>Ограничения масштабирования

Мониторы подключений имеют следующие ограничения масштабирования:

* Максимальное число мониторов подключения на подписку в одном регионе: 100
* Максимальное число групп тестов на монитор каждого подключения: 20
* Максимальное число источников и назначений на монитор подключения: 100
* Максимальное число конфигураций тестов на монитор подключений: 20 через ARMClient

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, [как анализировать данные мониторинга и настраивать оповещения](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts) .
* Узнайте [, как диагностировать проблемы в сети](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)

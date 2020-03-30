---
title: Настройка режима распределения Azure Load Balancer
titleSuffix: Azure Load Balancer
description: В этой статье приступить к настройке режима распределения для Azure Load Balancer для поддержки сродства IP-адреса.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023537"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Настройка режима распределения для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Режим распространения на основе хэша

По умолчанию в качестве режима распределения в Azure Load Balancer используется хэш с пятью кортежами. 

Туляк состоит из:
* **Источник IP**
* **Исходный порт**
* **Направление IP**
* **Порт назначения**
* **"Protocol type" (Тип протокола).**

Хэш используется для картирования трафика на доступные серверы. Алгоритм обеспечивает липкость только в ходе транспортного сеанса. Пакеты, наиболее наитое время сеанса, направляются на один и тот же IP-адрес центра обработки данных за сбалансированной нагрузкой конечной точкой. Когда клиент начинает новую сессию из того же IP-адреса источника, порт исходного кода изменяется и приводит к тому, что трафик переходит в другую конечную точку центра обработки данных.

![Режим распределения на основе хэша на основе пяти-тупл](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Режим сходства исходного IP-адреса

Балансиватор нагрузки также может быть настроен с помощью режима распределения сродства IP-кода. Он также известен как соответствие сеансу или соответствие клиентскому IP-адресу. Режим использует хэш для отображения трафика на доступных серверах (исходный IP и IP-адрес) или трехъярусный (источник IP, IP-адрес назначения и тип протокола) для отображения трафика на доступные серверы. Используя сродство исходного IP, соединения, начатые с одного и того же клиентского компьютера, переходят в ту же конечную точку центра обработки данных.

Следующая цифра иллюстрирует конфигурацию с двумя тупиками. Обратите внимание, как двухразовый баланспроходит через балансирусатор нагрузки на виртуальную машину 1 (VM1). Затем VM1 архивируется на виртуальных машинах VM2 и VM3.

![Режим распределения сродства двух-тупловых сеансов](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Режим соответствия исходному IP-адресу позволяет устранить несовместимость Azure Load Balancer и шлюза удаленных рабочих столов. С помощью этого режима можно создать ферму шлюза удаленных рабочих столов в одной облачной службе.

Другой сценарий использования — передача мультимедиа. В этом случае передача данных осуществляется по протоколу UDP, а управление — по протоколу TCP.

* Клиент начинает сеанс TCP с адресом, сбалансированным для нагрузки, и направляется на определенный DIP. Сам канал остается активным, чтобы контролировать состояние подключения.
* Новая сессия UDP с того же клиентского компьютера начинается с той же сбалансированной нагрузкой общедоступной конечной точки. Подключение направляется на ту же конечную точку DIP, что и предыдущее TCP-подключение. Таким образом можно передавать мультимедиа с более высокой пропускной способностью, поддерживая при этом канал управления по TCP.

> [!NOTE]
> При изменении набора балансировки нагрузки (при добавлении или удалении виртуальной машины) распределение запросов клиента вычисляется заново. Нельзя полагаться на то, что новые подключения от существующих клиентов будут направлены на один сервер. Кроме того, использование режима распределения соответствия исходному IP-адресу может привести к неравномерному распределению трафика. Клиенты, работающие на прокси-серверах, могут рассматриваться как одно уникальное клиентское приложение.

## <a name="configure-source-ip-affinity-settings"></a>Настройка параметров соответствия исходному IP-адресу

### <a name="azure-portal"></a>Портал Azure

Можно изменить конфигурацию режима распределения, изменив правило балансировки нагрузки в портале.

1. Войдите на портал Azure и найдите группу ресурсов, содержащую балансилер нагрузки, который вы хотите изменить, нажав на **группы ресурсов.**
2. На экране обзора баланса нагрузки нажмите на **правила балансировки нагрузки** в **настройках.**
3. На экране правил балансировки нагрузки нажмите на правило балансировки нагрузки, которое необходимо изменить режим распределения.
4. В соответствии с правилом режим распределения изменяется путем изменения **упорства сеанса.**  Доступны следующие варианты:
    
    * **Ни один (хэш-основе)** - Указывает, что последовательные запросы от одного и того же клиента могут обрабатываться любой виртуальной машиной.
    * **Клиент IP (источник IP сродство 2-tuple)** - Указывает, что последовательные запросы с того же клиента IP-адрес будет обрабатываться той же виртуальной машиной.
    * **Клиент IP и протокол (источник IP сродство 3-tuple)** - Указывает, что последовательные запросы от того же клиента IP-адрес и комбинация протокола будут обрабатываться той же виртуальной машиной.

5. Выберите режим распределения, а затем нажмите **Сохранить**.

### <a name="azure-powershell"></a>Azure PowerShell

Для виртуальных машин, развернутых с помощью менеджера ресурсов, используйте PowerShell для изменения параметров распределения грузоподъемности на существующее правило балансировки нагрузки. Следующая команда обновляет режим распределения: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Чтобы изменить параметры времени ожидания для виртуальных машин можно использовать Azure PowerShell. Добавьте конечную точку Azure в виртуальную машину и установите режим распределения балансировщика нагрузки.

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Установите значение `LoadBalancerDistribution` элемента для требуемого количества балансировки нагрузки. Укажите балансировку нагрузки sourceIP для двух-tuple (источник IP и IP назначения IP). Укажите балансировку нагрузки sourceIPProtocol для трех-tuple (источник IP, IP назначения и тип протокола). Укажите ни одного для поведения по умолчанию балансировки пяти-тупловых нагрузок.

Получите конфигурацию режима распределения балансировщика нагрузки для конечной точки с помощью этих параметров:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Когда `LoadBalancerDistribution` элемент не присутствует, Azure Load Balancer использует алгоритм пяти-туловища по умолчанию.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Настройка режима распределения для набора балансировки нагрузки для конечных точек

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует настроить режим распределения:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Настройка режима распределения для конечных точек облачной службы

Обновите облачную службу с помощью пакета SDK Azure для .NET 2.5. Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить режим распределения балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание.

Ниже приведен пример изменений в CSDEF-файле для настройки конечных точек:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Пример API

В следующем примере показано, как изменить режим распределения подсистемы балансировки нагрузки для заданного набора балансировки нагрузки в развертывании. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Изменение режима распределения развернутого набора балансировки нагрузки

Чтобы изменить существующую конфигурацию развертывания, используйте классическую модель развертывания Azure. Добавьте заголовок `x-ms-version` и задайте значение версии 2014-09-01 или более поздней.

#### <a name="request"></a>Запрос

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Как уже говорилось `LoadBalancerDistribution` ранее, установите элемент для sourceIP для сродства двух-tuple, sourceIPProtocol для сродства 3-tuple, или никакие для никакой сродства (сродство 5-tuple).

#### <a name="response"></a>Ответ

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)
* [Создание балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell](quickstart-create-standard-load-balancer-powershell.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: Настройка режима распределения Azure Load Balancer
titleSuffix: Azure Load Balancer
description: В этой статье приступите к настройке режима распределения для Azure Load Balancer для поддержки сопоставления исходного IP-адреса.
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
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225378"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Настройка режима распределения для Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Режим распространения на основе хэша

Режим распределения по умолчанию для Azure Load Balancer — это хэш из пяти кортежей. 

Кортеж состоит из следующих элементов:
* **Исходный IP-адрес**
* **Исходный порт**
* **Конечный IP-адрес**
* **Конечный порт**
* **Тип протокола**

Хэш используется для отображения трафика на доступные серверы. Алгоритм обеспечивает прикрепление только в рамках транспортного сеанса. Пакеты, находящиеся в одном сеансе, направляются в один и тот же IP-адрес центра обработки данных за конечную точку с балансировкой нагрузки. Когда клиент запускает новый сеанс из того же исходного IP-адреса, исходный порт изменяется и вызывает переход трафика в другую конечную точку центра обработки данных.

![Режим распределения с использованием хэша из пяти кортежей](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Режим сходства исходного IP-адреса

Подсистема балансировки нагрузки также может быть настроена с использованием режима распределения сходства исходного IP-адреса. Он также известен как соответствие сеансу или соответствие клиентскому IP-адресу. В режиме используется хэш с двумя кортежами (исходный IP-адрес и IP-адрес назначения) или с тремя кортежами (исходный IP-адрес, IP-адрес назначения и тип протокола) для отображения трафика на доступные серверы. При использовании сходства исходного IP-адреса подключения, запущенные с одного клиентского компьютера, отправляются в одну конечную точку центра обработки данных.

На следующем рисунке показана конфигурация из двух кортежей. Обратите внимание, как два кортежа выполняются через подсистему балансировки нагрузки к виртуальной машине 1 (VM1). Затем VM1 архивируется на виртуальных машинах VM2 и VM3.

![Режим распределения сходства сеансов с двумя кортежами](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Режим соответствия исходному IP-адресу позволяет устранить несовместимость Azure Load Balancer и шлюза удаленных рабочих столов. С помощью этого режима можно создать ферму шлюза удаленных рабочих столов в одной облачной службе.

Другой сценарий использования — передача мультимедиа. В этом случае передача данных осуществляется по протоколу UDP, а управление — по протоколу TCP.

* Клиент запускает сеанс TCP для общедоступного адреса с балансировкой нагрузки и направляется на конкретный DIP. Сам канал остается активным, чтобы контролировать состояние подключения.
* Новый сеанс UDP с того же клиентского компьютера запускается на той же общедоступной конечной точке с балансировкой нагрузки. Подключение направляется на ту же конечную точку DIP, что и предыдущее TCP-подключение. Таким образом можно передавать мультимедиа с более высокой пропускной способностью, поддерживая при этом канал управления по TCP.

> [!NOTE]
> При изменении набора балансировки нагрузки (при добавлении или удалении виртуальной машины) распределение запросов клиента вычисляется заново. Нельзя полагаться на то, что новые подключения от существующих клиентов будут направлены на один сервер. Кроме того, использование режима распределения соответствия исходному IP-адресу может привести к неравномерному распределению трафика. Клиенты, работающие на прокси-серверах, могут рассматриваться как одно уникальное клиентское приложение.

## <a name="configure-source-ip-affinity-settings"></a>Настройка параметров соответствия исходному IP-адресу

### <a name="azure-portal"></a>портале Azure

Можно изменить конфигурацию режима распределения, изменив правило балансировки нагрузки на портале.

1. Войдите в портал Azure и выберите группу ресурсов, содержащую подсистему балансировки нагрузки, которую вы хотите изменить, щелкнув **группы ресурсов**.
2. На экране "Обзор балансировщика нагрузки" щелкните **правила балансировки нагрузки** в разделе **Параметры**.
3. На экране правила балансировки нагрузки щелкните правило балансировки нагрузки, которое необходимо изменить в режиме распространения.
4. В правиле режим распределения изменяется путем изменения раскрывающегося списка **сохраняемость сеанса** .  Доступны следующие варианты:
    
    * **Нет (на основе хэша)** . указывает, что последовательные запросы от одного клиента могут обрабатываться любой виртуальной машиной.
    * **IP-адрес клиента (сопоставление ИСХОДНОГО IP-адреса 2 — кортеж)** . указывает, что последовательные запросы с одного и того же IP-адресов клиента будут обрабатываться одной и той же виртуальной машиной.
    * **IP-адрес клиента и протокол (сходство исходного IP-адреса 3 — кортеж)** . указывает, что последовательные запросы с одинаковой КОМБИНАЦИЕЙ IP-адресов и протоколов клиента будут обрабатываться одной и той же виртуальной машиной.

5. Выберите режим распространения и нажмите кнопку **сохранить**.

### <a name="azure-powershell"></a>Azure PowerShell

Для виртуальных машин, развернутых с диспетчер ресурсов, используйте PowerShell, чтобы изменить параметры распределения балансировки нагрузки в существующем правиле балансировки нагрузки. Следующая команда обновляет режим распределения: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Чтобы изменить параметры времени ожидания для виртуальных машин можно использовать Azure PowerShell. Добавьте конечную точку Azure в виртуальную машину и установите режим распределения балансировщика нагрузки.

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Установите значение элемента `LoadBalancerDistribution` в поле требуемый объем балансировки нагрузки. Укажите sourceIP для балансировки нагрузки двух кортежей (исходный IP-адрес и IP-адрес назначения). Укажите sourceIPProtocol для балансировки нагрузки трех кортежей (исходный IP-адрес, IP-адрес назначения и тип протокола). Укажите None в качестве поведения по умолчанию для балансировки нагрузки с пятью кортежами.

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

Если элемент `LoadBalancerDistribution` отсутствует, Azure Load Balancer использует алгоритм с пятью кортежами по умолчанию.

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

#### <a name="request"></a>Request

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

Как было описано выше, установите элемент `LoadBalancerDistribution` в sourceIP для сходства двух кортежей, sourceIPProtocol для сходства с тремя кортежами или None для отсутствия сходства (сходство с пятью кортежами).

#### <a name="response"></a>Ответ

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Дополнительная информация

* [Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)
* [Создание балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell](load-balancer-get-started-internet-arm-ps.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: Схема аналитики трафика Azure (ru) Документы Майкрософт
description: Понять схему анализа аналитики трафика для анализа журналов потоков группы сетевой безопасности Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666381"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Схема и агрегация данных в анализе трафика

"Аналитика трафика" — это облачное решение, которое позволяет следить за действиями пользователя и приложения в облачных сетях. Это решение анализирует журналы потоков группы безопасности сети (NSG) Наблюдателя за сетями, чтобы предоставить сведения о потоке трафика в облаке Azure. Решение "Аналитика трафика" позволяет выполнять следующее:

- Визуализировать сетевую активность в подписках Azure и определять самые активные узлы.
- Выявлять угрозы безопасности в сети и защитить ее инфраструктуру на основе сведений об открытых портах, приложениях, пытающихся получить доступ к Интернету, а также виртуальных машинах, подключающихся к несанкционированным сетям.
- Понять шаблоны потока трафика в регионах Azure и Интернете, чтобы оптимизировать развертывание сети для лучшей производительности и эффективного использования емкости.
- Оперативно обнаружить неверные сетевые конфигурации, которые приводят к сбоям подключений в сети.
- Знайте использование сети в байтах, пакетах или потоках.

### <a name="data-aggregation"></a>Агрегация данных

1. Все журналы потока в NSG между "FlowIntervalStartTime_t" и "FlowIntervalEndTime_t" фиксируются с интервалом в одну минуту в учетной записи хранилища в виде капли перед обработкой Traffic Analytics.
2. Интервал обработки трафика по умолчанию составляет 60 минут. Это означает, что каждые 60 минут Traffic Analytics выбирает капли из хранилища для агрегации. Если выбранный интервал обработки составляет 10 минут, Traffic Analytics будет выбирать капли из учетной записи хранения после каждых 10 минут.
3. Потоки, которые имеют тот же ИС-источник, destination IP, порт назначения, имя NSG, правило NSG, направление потока и протокол уровня транспорта (TCP или UDP) (Примечание: Порт источника исключен для агрегирования) закладываются в единый поток по трафику Analytics
4. Эта единственная запись украшена (Подробности в разделе ниже) и попадает в журнал Analytics по трафику Analytics.This процесс может занять до 1 часа макс.
5. FlowStartTime_t поле указывает на первое появление такого агрегированного потока (тот же четырех-туловище) в интервале обработки потока между "FlowIntervalStartTime_t" и "FlowIntervalEndTime_t".
6. Для любого ресурса в TA потоки, указанные в пользовательском интерфейсе, являются общими потоками, видимыми NSG, но в журнале Analytics пользователь увидит только одну уменьшенную запись. Чтобы увидеть все потоки, используйте поле blob_id, на которое можно ссылаться из Хранилища. Общий показатель потока для этой записи будет соответствовать отдельным потокам, видимым в каплей.

Ниже приведенный запрос поможет вам просмотреть все журналы потока из помещений за последние 30 дней.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Для просмотра пути кабы для потоков в вышеупомянутом запросе используйте нижеуказанный запрос:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Вышеупомянутый запрос конструирует URL для доступа к каплям напрямую. URL-адрес с держателями мест приведен ниже:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Поля, используемые в схеме анализа трафика
  > [!IMPORTANT]
  > Схема анализа трафика была обновлена 22 августа 2019 года. Новая схема предоставляет исегом и испационные центры назначения, отдельно устраняющие необходимость разбора поля FlowDirection, упрощающие запросы. </br>
  > FASchemaVersion_s обновлено от 1 до 2. </br>
  > Обеззараженные поля: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Новые поля: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Обеззараженные поля будут доступны до 22 ноября 2019 года.

Аналитика трафика построена на вершине журнала Analytics, так что вы можете запустить пользовательские запросы на данные, украшенные трафик омрачает и набор оповещений на том же.

Ниже перечислены поля в схеме и то, что они обозначает

| Поле | Формат | Комментарии |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Таблица для данных анализа трафика
| SubType_s | FlowLog | Подтип для журналов потока. Используйте только "FlowLog", другие значения SubType_s для внутренней работы продукта |
| FASchemaVersion_s |   2   | Версия схемы. Не отражает версию журнала NSG Flow Log |
| TimeProcessed_t   | Дата и время в UTC  | Время, в течение которого аналитика трафика обрабатывает журналы необработанных потоков из учетной записи хранилища |
| FlowIntervalStartTime_t | Дата и время в UTC |  Время начала интервала обработки журнала потока. Это время, с которого измеряется интервал потока |
| FlowIntervalEndTime_t | Дата и время в UTC | Время окончания интервала обработки журнала потока |
| FlowStartTime_t | Дата и время в UTC |  Первое возникновение потока (который будет агрегирован) в интервале обработки потока между "FlowIntervalStartTime_t" и "FlowIntervalEndTime_t". Этот поток агрегируется на основе логики агрегирования |
| FlowEndTime_t | Дата и время в UTC | Последнее возникновение потока (который будет агрегирован) в интервале обработки потока между "FlowIntervalStartTime_t" и "FlowIntervalEndTime_t". С точки зрения потока журнала v2, это поле содержит время, когда последний поток с той же четырех-tuple начал (отмеченкаки как "B" в записи необработанного потока) |
| FlowType_s |  ИнтраВНет <br> - ИнтерВНет <br> S2S <br> P2S <br> - AzurePublic <br> - ВнешняяОбщественность <br> - MaliciousFlow <br> - Неизвестный рядовой <br> Неизвестный | Определение в примечаниях ниже таблицы |
| SrcIP_s | Исходный IP-адрес | Будет пустой в случае AzurePublic и внешнихобщественных потоков |
| DestIP_s | Конечный IP-адрес | Будет пустой в случае AzurePublic и внешнихобщественных потоков |
| VMIP_s | IP of the VM | Используется для потоков AzurePublic и ExternalPublic |
| PublicIP_s | Общедоступные IP-адреса | Используется для потоков AzurePublic и ExternalPublic |
| DestPort_d | Конечный порт | Порт, в котором входящий трафик |
| L4Protocol_s  | - T <br> U U  | Транспортный протокол. ТКП <br> U и UDP |
| L7Protocol_s  | Имя протокола | Произведено из порта назначения |
| FlowDirection_s | Входящие я входящих<br> O - Выход | Направление потока в/из NSG в журнале потока |
| FlowStatus_s  | A - Разрешено правилом NSG <br> D - Отказано в правиле NSG  | Статус потока разрешено/заблокировано NSG в журнале потока |
| NSGList_s | \<>>\/ NSG_NAME \/>>>>>>><RESOURCEGROUP_NAME<>>>> | Группа сетевой безопасности (NSG), связанная с потоком |
| NSGRules_s | \<Значение индекса \| \<0)>NSG_RULENAME \| \<направление \| \<>\| \<потока>состояние потока>> |  Правило NSG, которое позволяло или отрицало этот поток |
| NSGRule_s | NSG_RULENAME |  Правило NSG, которое позволяло или отрицало этот поток |
| NSGRuleType_s | Пользователь, определяемый по умолчанию |   Тип правила NSG, используемого потоком |
| MACAddress_s | MAC-адрес | MAC адрес NIC, на котором поток был захвачен |
| Subscription_s | Подписка виртуального сетевого/ сетевого интерфейса Azure/ виртуальной машины населена в этой области | Применяется только для FlowType s S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow и UnknownPrivate типы потоков (типы потока, где только одна сторона лазурна) |
| Subscription1_s | Идентификатор подписки | Идентификатор подписки виртуальной сети/сетевого интерфейса/виртуальной машины, к которой принадлежит исходный IP в потоке |
| Subscription2_s | Идентификатор подписки | Идентификатор подписки виртуальной сети/сетевого интерфейса/виртуальной машины, к которой принадлежит IP назначения в потоке |
| Region_s | Azure области виртуального сетевого/ сетевого интерфейса / виртуальной машины, к которой принадлежит IP в потоке | Применяется только для FlowType s S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow и UnknownPrivate типы потоков (типы потока, где только одна сторона лазурна) |
| Region1_s | Регион Azure | Azure области виртуального сетевого/ сетевого интерфейса / виртуальной машины, к которой источник IP в потоке принадлежит |
| Region2_s | Регион Azure | Azure области виртуальной сети, к которой назначения IP в потоке принадлежит |
| NIC_s | \<resourcegroup_Name \/ \<>> NetworkInterfaceName |  NIC, связанный с отправкой или получением трафика VM |
| NIC1_s | <resourcegroup_Name\<>/NetworkInterfaceName> | NIC, связанный с исходным IP в потоке |
| NIC2_s | <resourcegroup_Name\<>/NetworkInterfaceName> | NIC, связанный с ИС назначения в потоке |
| VM_s | <resourcegroup_Name \/ \<>InterfaceName> | Виртуальная машина, связанная с интерфейсом сети NIC_s |
| VM1_s | <resourcegroup_Name\<>/> VirtualMachineName | Виртуальная машина, связанная с исходным IP в потоке |
| VM2_s | <resourcegroup_Name\<>/> VirtualMachineName | Виртуальная машина, связанная с IP-адресом в потоке |
| Subnet_s | <ResourceGroup_Name>/<\<VNET_Name>>/SubnetName> | Подсеть, связанная с NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<\<VNET_Name>>/SubnetName> | Подсеть, связанная с Исходным IP в потоке |
| Subnet2_s | <ResourceGroup_Name>/<\<VNET_Name>>/SubnetName>    | Подсеть, связанная с IP-адресом назначения в потоке |
| ApplicationGateway1_s | \<Подписка>/\<ResourceGroupName\<>/ ApplicationGatewayName> | Шлюз приложения, связанный с Ip-адресом Источника в потоке |
| ApplicationGateway2_s | \<Подписка>/\<ResourceGroupName\<>/ ApplicationGatewayName> | Шлюз приложения, связанный с IP-адресом назначения в потоке |
| LoadBalancer1_s | \<Подписка>/\<ResourceGroupName\<>/ LoadBalancerName> | Балансиватор нагрузки, связанный с Ip-адресом Источника в потоке |
| LoadBalancer2_s | \<Подписка>/\<ResourceGroupName\<>/ LoadBalancerName> | Балансиватор нагрузки, связанный с IP-адресом назначения в потоке |
| LocalNetworkGateway1_s | \<Подписка>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | Локальный сетевой шлюз, связанный с Ip-адресом Источника в потоке |
| LocalNetworkGateway2_s | \<Подписка>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | Локальный сетевой шлюз, связанный с IP-адресом назначения в потоке |
| ConnectionType_s | Возможные значения: VNetPeering, VpnGateway и ExpressRoute |    Тип соединения |
| ConnectionName_s | \<Подписка>/\<ResourceGroupName\<>/ ConnectionName> | Имя подключения. Для типа потока P2S, это <gateway name>будет отформатировано как<VPN Client IP> |
| ConnectingVNets_s | Пространство разделено список виртуальных названий сетей | В случае концентратора и спица топологии, здесь будут заселены виртуальные сети концентратора |
| Country_s | Код двух букв (ISO 3166-1 альфа-2) | Населенный для потока типа ExternalPublic. Все IP-адреса в поле PublicIPs_s будут совместно с тем же кодом страны |
| AzureRegion_s | Расположение регионов Azure | Населенный для потока типа AzurePublic. Все IP-адреса в поле PublicIPs_s будут делиться областью Azure |
| AllowedInFlows_d | | Количество разрешенных входящих потоков. Это представляет собой количество потоков, которые разделяют те же четыре-tuple входящих в сетевом интерфейсе, на котором поток был захвачен |
| DeniedInFlows_d |  | Граф входящих потоков, которые были отклонены. (Вход в сетевой интерфейс, при котором был захвачен поток) |
| AllowedOutFlows_d | | Количество разрешенных исходящих потоков (выход в сетевой интерфейс, при котором был захвачен поток) |
| DeniedOutFlows_d  | | Отсчет исходящих потоков, которые были отклонены (выход в сетевой интерфейс, при котором поток был захвачен) |
| FlowCount_d | Не рекомендуется. Общие потоки, которые соответствовали тем же четырем тупл. В случае типов потоков ExternalPublic и AzurePublic, подсчет будет включать потоки с различных адресов PublicIP.
| InboundPackets_d | Пакеты, полученные как захваченные в сетевом интерфейсе, где применялось правило NSG | Это заполняется только для версии 2 схемы журнала потока NSG |
| OutboundPackets_d  | Пакеты, отправленные как захваченные в сетевом интерфейсе, где применялось правило NSG | Это заполняется только для версии 2 схемы журнала потока NSG |
| InboundBytes_d |  Байты, полученные как захваченные в сетевом интерфейсе, где применялось правило NSG | Это заполняется только для версии 2 схемы журнала потока NSG |
| OutboundBytes_d | Байты, отправленные как захваченные в сетевом интерфейсе, где применялось правило NSG | Это заполняется только для версии 2 схемы журнала потока NSG |
| CompletedFlows_d  |  | Это заполняется с ненулевым значением только для версии 2 схемы журнала потока NSG |
| PublicIPs_s | <>\| \<>\| \<>\| \<>\| \<>\| \<>\|>>>>INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS>>>>>>> INBOUND_BYTES>\<OUTBOUND_BYTES>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>>>>>PUBLIC_IP> | Записи, разделенные барами |
| SrcPublicIPs_s | \| \<>\| \< \| \< \| \<>\<FLOW_ENDED_COUNT FLOW_STARTED_COUNT><INBOUND_PACKETS>>>SOURCE_PUBLIC_IP SOURCE_PUBLIC_IP SOURCE_PUBLIC_IP>>OUTBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS> INBOUND_BYTES OUTBOUND_BYTES>INBOUND_PACKETS>>>>>\| \| \< | Записи, разделенные барами |
| DestPublicIPs_s | <<\| \<DESTINATION_PUBLIC_IP \| \<>\| \<DESTINATION_PUBLIC_IP \| \<FLOW_STARTED_COUNT \| \<>\| \<>>INBOUND_PACKETS>FLOW_ENDED_COUNT>>>>>OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES INBOUND_BYTES> | Записи, разделенные барами |

### <a name="notes"></a>Примечания

1. В случае потоков AzurePublic и ExternalPublic, принадлежащий клиенту Azure VM IP, заселен в VMIP_s поле, в то время как общедоступные IP-адреса заполняются в PublicIPs_s поле. Для этих двух типов потоков мы должны использовать VMIP_s и PublicIPs_s, а не SrcIP_s и DestIP_s поля. Для адресов AzurePublic и ExternalPublicIP мы комгрегируем далее, так что количество записей, попавших в рабочее пространство аналитики журналов клиентов, является минимальным. (Это поле будет унижается в ближайшее время, и мы должны использовать SrcIP_ и DestIP_s в зависимости от того, лазурный VM был источником или назначения в потоке)
1. Подробная информация о типах потоков: На основе IP-адресов, участвующих в потоке, мы классифицируем потоки в следующих типах потока:
1. IntraVNet - Оба IP-адреса в потоке находятся в той же виртуальной сети Azure.
1. InterVNet - IP-адреса в потоке находятся в двух разных виртуальных сетях Azure.
1. S2S - (Site To Site) Один из IP-адресов принадлежит виртуальной сети Azure, в то время как другой IP-адрес принадлежит сети клиентов (Site), подключенной к виртуальной сети Azure через VPN шлюз или Экспресс-маршрут.
1. P2S - (Точка на сайт) Один из IP-адресов принадлежит виртуальной сети Azure, в то время как другой IP-адрес принадлежит сети клиентов (Site), подключенной к виртуальной сети Azure через VPN шлюз.
1. AzurePublic - Один из IP-адресов принадлежит виртуальной сети Azure, а другой IP-адрес — IP-адресам Azure Internal Public IP-адресам, принадлежащим корпорации Майкрософт. Государственные IP-адреса, принадлежащие клиентам, не будут частью этого типа потока. Например, любой клиент, принадлежащий VM, отправляющий трафик в службу Azure (конечная точка хранения), будет классифицирован под этим типом потока.
1. ExternalPublic - Один из IP-адресов принадлежит виртуальной сети Azure, в то время как другой IP-адрес является общедоступным IP, который не находится в Azure, не сообщается как вредоносный в каналах ASC, которые Traffic Analytics потребляет для интервала обработки между " FlowIntervalStartTime_t" и "FlowIntervalEndTime_t".
1. MaliciousFlow - Один из IP-адресов принадлежит лазурной виртуальной сети, в то время как другой IP-адрес является публичным IP, который не находится в Azure и сообщается как вредоносный в каналах ASC, которые Traffic Analytics потребляет для интервала обработки между " FlowIntervalStartTime_t" и "FlowIntervalEndTime_t".
1. UnknownPrivate - Один из IP-адресов принадлежит виртуальной сети Azure, а другой IP-адрес принадлежит частному IP-диапазону, как это определено в RFC 1918, и не может быть отображен Traffic Analytics на принадлежащий клиенту сайт или виртуальную сеть Azure.
1. Неизвестный — Не удается сопоставить ни один из IP-адресов в потоках с топологией клиента в Azure, а также на местах (сайт).
1. Некоторые полевые названия \_придатится с s или \_d. Они НЕ означают источник и назначение, но указывают на строку типов данных и десятичную соответственно.

### <a name="next-steps"></a>Next Steps
Чтобы получить ответы на часто задаваемые вопросы, [Traffic analytics documentation](traffic-analytics.md) см. [Traffic analytics FAQ](traffic-analytics-faq.md)

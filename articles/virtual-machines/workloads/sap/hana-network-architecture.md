---
title: Сетевая архитектура SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт
description: Сетевая архитектура развертывания SAP HANA в Azure (крупные экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3777180a4d62f8b253ac4cd096bff15613f33565
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206626"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Сетевая архитектура SAP HANA в Azure (крупные экземпляры)

Архитектура сетевых служб Azure является ключевым компонентом для успешного развертывания приложений SAP в крупных экземплярах HANA. Как правило, развернутое решение "SAP HANA в Azure (крупные экземпляры)" отличается большим ландшафтом SAP, который включает в себя несколько разных решений SAP с различными размерами баз данных, а также показателями потребления ресурсов ЦП и памяти. It's likely that not all IT systems are located in Azure already. Your SAP landscape is often hybrid as well from a DBMS point and SAP application point of view using a mixture of NetWeaver, and S/4HANA and SAP HANA and other DBMS. Azure offers different services that allow you to run the different DBMS, NetWeaver, and S/4HANA systems in Azure. Azure also offers you network technology to make Azure look like a virtual data center to your on-premises software deployments

Unless your complete IT systems are hosted in Azure. Azure networking functionality is used to connect the on-premises world with your Azure assets to make Azure look like a virtual datacenter of yours. The Azure network functionality used is: 

- Azure virtual networks are connected to the [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit that connects to your on-premises network assets.
- An ExpressRoute circuit that connects on-premises to Azure should have a minimum bandwidth of [1 Gbps or higher](https://azure.microsoft.com/pricing/details/expressroute/). Это позволяет обеспечить достаточную пропускную способность для передачи данных между локальными системами и системами, запущенными на виртуальных машинах, а также подключение к системам Azure из локальной среды конечных пользователей.
- All SAP systems in Azure are set up in virtual networks to communicate with each other.
- Active Directory and DNS hosted on-premises are extended into Azure through ExpressRoute from on-premises, or are running complete in Azure.

For the specific case of integrating HANA Large Instances into the Azure data center network fabric, Azure ExpressRoute technology is used as well


> [!NOTE] 
> Only one Azure subscription can be linked to only one tenant in a HANA Large Instance stamp in a specific Azure region. Conversely, a single HANA Large Instance stamp tenant can be linked to only one Azure subscription. Этот факт не отличается от любых других объектов в Azure, которые подлежат оплате.

If SAP HANA on Azure (Large Instances) is deployed in multiple different Azure regions, a separate tenant is deployed in the HANA Large Instance stamp. Оба клиента могут выполняться в одной подписке Azure, если эти экземпляры являются частью одного ландшафта SAP. 

> [!IMPORTANT] 
> Only the Azure Resource Manager deployment method is supported with SAP HANA on Azure (Large Instances).

 

## <a name="additional-virtual-network-information"></a>Дополнительные сведения о виртуальной сети

To connect a virtual network to ExpressRoute, an Azure ExpressRoute gateway must be created. For more information, see [About Expressroute gateways for ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

An Azure ExpressRoute gateway is used with ExpressRoute to an infrastructure outside of Azure or to an Azure Large Instance stamp. You can connect the Azure ExpressRoute gateway to a maximum of four different ExpressRoute circuits as long as those connections come from different Microsoft enterprise edge routers. Дополнительные сведения см. в статье [Инфраструктура и возможности подключения SAP HANA в Azure (крупные экземпляры)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> The maximum throughput you can achieve with a ExpressRoute gateway is 10 Gbps by using an ExpressRoute connection. При копировании файлов между виртуальной машиной, расположенной в виртуальной сети, и локальной системой (в рамках одного потока копирования) невозможно достичь полной пропускной способности шлюза с разными номерами SKU. To leverage the complete bandwidth of the ExpressRoute gateway, use multiple streams. Также можно копировать разные файлы с помощью параллельных потоков одного файла.


## <a name="networking-architecture-for-hana-large-instance"></a>Архитектура сети для крупных экземпляров HANA
Архитектуру сети для крупных экземпляров HANA можно разделить на четыре части:

- Локальная сеть и подключение ExpressRoute к Azure. Эта часть является доменом пользователей и подключается к Azure через ExpressRoute. This Expressroute circuit is fully paid by you as a customer. The bandwidth should be large enough to handle the network traffic between your on-premises assets and the Azure region you are connecting against. См. правый нижний угол на схеме ниже.
- Azure network services, as previously discussed, with virtual networks, which again need ExpressRoute gateways added. В этой области необходимо определить подходящую структуру в соответствии с требованиями приложений, безопасности и нормативных требований. При использовании крупных экземпляров HANA также необходимо выбрать число виртуальных сетей и SKU шлюзов Azure. См. правый верхний угол на схеме.
- Подключение крупных экземпляров HANA к Azure с помощью технологии ExpressRoute. Эту часть развертывает и обслуживает корпорация Майкрософт. Вам достаточно предоставить диапазоны IP-адресов и после развертывания ресурсов в крупных экземплярах HANA подключить канал ExpressRoute к виртуальным сетям. Дополнительные сведения см. в статье [Инфраструктура и возможности подключения SAP HANA в Azure (крупные экземпляры)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). There is no additional fee for you as a customer for the connectivity between the Azure data center network fabric and HANA Large Instance units.
- Networking within the HANA Large Instance stamp, which is mostly transparent for you.

![Виртуальная сеть подключена к SAP HANA в Azure (крупные экземпляры) и к локальной сети](./media/hana-overview-architecture/image1-architecture.png)

Использование крупных экземпляров HANA не отменяет требование подключать локальные ресурсы к Azure через ExpressRoute. Также требуется иметь одну или несколько виртуальных сетей с виртуальными машинами, на которых размещен уровень приложений, который подключается к экземплярам HANA, размещенным в единицах крупных экземпляров HANA. 

Отличия развертываний SAP только в Azure заключаются в следующем:

- Единицы крупных экземпляров HANA пользовательского клиента соединены через другой канал ExpressRoute с виртуальными сетями. To separate load conditions, the on-premises to Azure virtual network ExpressRoute circuits and the circuits between Azure virtual networks and HANA Large Instances don't share the same routers.
- Профиль рабочей нагрузки между уровнем приложений SAP и крупным экземпляром HANA является другой причиной возникновения множества небольших запросов и пиков передачи данных (результирующие наборы) между SAP HANA и уровнем приложений.
- Архитектура приложения SAP более чувствительна к задержкам в сети, чем в типичных сценариях, где обмен данными происходит между локальной средой и Azure.
- The Azure ExpressRoute gateway has at least two ExpressRoute connections. One circuit that is connected from on-premises and one that is connected from HANA Large Instances. This leaves only room for another two additional circuits from different MSEEs to connect to on ExpressRoute Gateway. This restriction is independent of the usage of ExpressRoute Fast Path. All the connected circuits share the maximum bandwidth for incoming data of the ExpressRoute gateway.

With Revision 3 of HANA Large Instance stamps, the network latency experienced between VMs and HANA Large Instance units can be higher than a typical VM-to-VM network round-trip latency. В зависимости от региона Azure значения задержки приема-передачи могут превышать 0,7 мс. Такие значения классифицируются как значения ниже среднего в [примечании к SAP № 1100926 с часто задаваемыми вопросами к производительности сети](https://launchpad.support.sap.com/#/notes/1100926/E). Измеренная задержка может достигать до 2 миллисекунд. Этот показатель зависит от региона Azure и средства измерения задержки приема-передачи в сети между виртуальной машиной Azure и единицей крупного экземпляра HANA. Тем не менее клиенты успешно развертывают производственные приложения SAP на основе SAP HANA в крупных экземплярах SAP HANA. Следует тщательно протестировать бизнес-процессы в крупных экземплярах HANA в Azure. A new functionality, called ExpressRoute Fast Path, is able to reduce the network latency between HANA Large Instances and application layer VMs in Azure substantially (see below). 

With Revision 4 of HANA Large Instance stamps, the network latency between Azure VMs that are deployed in proximity to the HANA Large Instance stamp, is experienced to meet the average or better than average classification as documented in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) if Azure ExpressRoute Fast Path is configured (see below). In order to deploy Azure VMs in close proximity to HANA Large Instance units of Revision 4, you need to leverage [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). The way how proximity placement groups can be used to locate the SAP application layer in the same Azure datacenter as Revision 4 hosted HANA Large Instance units is described in [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).

To provide deterministic network latency between VMs and HANA Large Instance, the choice of the ExpressRoute gateway SKU is essential. В отличие от шаблонов трафика между локальными и виртуальными машинами, шаблон трафика между виртуальными машинами и крупными экземплярами HANA может показывать короткий, но высокий пик запросов и объемов передаваемых данных. Для нормальной обработки таких пиков рекомендуется использовать шлюз с номером SKU UltraPerformance. For the Type II class of HANA Large Instance SKUs, the use of the UltraPerformance gateway SKU as a ExpressRotue gateway is mandatory.

> [!IMPORTANT] 
> Учитывая общий сетевой трафик между уровнями базы данных и приложений SAP, для подключения виртуальных сетей к SAP HANA в Azure (крупные экземпляры) поддерживается только шлюз с номером SKU HighPerformance или UltraPerformance. For HANA Large Instance Type II SKUs, only the UltraPerformance gateway SKU is supported as a ExpressRoute gateway. Exceptions apply when using ExpressRoute Fast Path (see below)

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
To lower the latency, ExpressRoute Fast Path got introduced and released in May 2019 for the specific connectivity of HANA Large Instances to Azure virtual networks that host the SAP application VMs. The major difference to the solution rolled out so far, is, that the data flows between VMs and HANA Large Instances are not routed through the ExpressRoute gateway anymore. Instead the VMs assigned in the subnet(s) of the Azure virtual network are directly communicating with the dedicated enterprise edge router. 

> [!IMPORTANT] 
> The ExpressRoute Fast Path functionality requires that the subnets running the SAP application VMs are in the same Azure virtual network that got connected to the HANA Large Instances. VMs located in Azure virtual networks that are peered with the Azure virtual network connected directly to the HANA Large Instance units are not benefiting from ExpressRoute Fast Path. As a result typical hub and spoke virtual network designs, where the ExpressRoute circuits are connecting against a hub virtual network and virtual networks containing the SAP application layer (spokes) are getting peered, the optimization by ExpressRoute Fast Path will not work. In addtion, ExpressRoute Fast Path does not support user defined routing rules (UDR) today. For more information, see [ExpressRoute virtual network gateway and FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


For more details on how to configure ExpressRoute Fast Path, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> An UltraPerformance ExpressRoute gateway is required to have ExpressRoute Fast Path working


## <a name="single-sap-system"></a>Одна система SAP

Локальная инфраструктура, показанная выше, подключена к Azure через канал ExpressRoute. The ExpressRoute circuit connects into a Microsoft enterprise edge router (MSEE). Дополнительные сведения см. в статье [Обзор ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). After the route is established, it connects into the Azure backbone.

> [!NOTE] 
> Для работы с ландшафтом SAP в Azure выполните подключение к маршрутизатору MSEE, который расположен ближе других к региону Azure в пределах ландшафта SAP. HANA Large Instance stamps are connected through dedicated enterprise edge router devices to minimize network latency between VMs in Azure IaaS and HANA Large Instance stamps.

The ExpressRoute gateway for the VMs that host SAP application instances are connected to one ExpressRoute circuit that connects to on-premises. Та же виртуальная сеть подключена к отдельному маршрутизатору MSEE, предназначенному для подключения к стекам крупных экземпляров. Using ExpressRoute Fast Path, the data flow from HANA Large Instances to the SAP application layer VMs are not routed through the ExpressRoute gateway anymore and with that reduce the network round-trip latency.

Это простой пример отдельной системы SAP. В ней уровень приложений SAP размещен в Azure. База данных SAP HANA выполняется в SAP HANA в Azure (крупные экземпляры). The assumption is that the ExpressRoute gateway bandwidth of 2-Gbps or 10-Gbps throughput doesn't represent a bottleneck.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Несколько обычных или крупных систем SAP

If multiple SAP systems or large SAP systems are deployed to connect to SAP HANA on Azure (Large Instances), the throughput of the ExpressRoute gateway might become a bottleneck. Or you want to isolate production and non-production systems in different Azure virtual networks. В этом случае необходимо разбить уровни приложений на несколько виртуальных сетей. Также рекомендуется создать специальные виртуальные сети, которые подключаются к крупным экземплярам HANA в таких случаях:

- Выполнение архивации непосредственно из экземпляров HANA в крупных экземплярах HANA на виртуальную машину в Azure, где размещаются общие ресурсы NFS.
- Копирование больших резервных копий и других файлов из крупных экземпляров HANA на диск, управляемый в Azure.

Use a separate virtual network to host VMs that manage storage for mass transfer of data between HANA Large Instances and Azure. This arrangement avoids the effects of large file or data transfer from HANA Large Instance to Azure on the ExpressRoute gateway that serves the VMs that run the SAP application layer. 

Для более масштабируемой сетевой архитектуры:

- Используйте несколько виртуальных сетей для одного большего уровня приложений SAP.
- Вместо объединения систем SAP из разных подсетей в одну виртуальную сеть разверните отдельную виртуальную сеть для каждой развернутой системы SAP.

  Более масштабируемая сетевая архитектура для SAP HANA в Azure (крупные экземпляры):

![Развертывание уровня приложений SAP в нескольких виртуальных сетях](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependent on the rules and restrictions, you want to apply between the different virtual networks hosting VMs of different SAP systems, you should peer those virtual networks. Дополнительные сведения о пиринге виртуальных сетей см. в [этой статье](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Маршрутизация в Azure

By default deployment, three network routing considerations are important for SAP HANA on Azure (Large Instances):

* SAP HANA on Azure (Large Instances) can be accessed only through Azure VMs and the dedicated ExpressRoute connection, not directly from on-premises. Прямой доступ из локальной среды к единицам крупных экземпляров HANA, поставляемым корпорацией Майкрософт, сразу невозможен. Это ограничения переходной маршрутизации текущей архитектуры сети Azure, используемой для крупных экземпляров SAP HANA. Таким образом, клиенты администрирования и любые приложения, требующие прямого доступа, например запущенный локально SAP Solution Manager, не могут подключиться к базе данных SAP HANA. For exceptions check the section 'Direct Routing to HANA Large Instances'.

* If you have HANA Large Instance units deployed in two different Azure regions for disaster recovery, the same transient routing restrictions applied in the past. In other words, IP addresses of a HANA Large Instance unit in one region (for example, US West) were not routed to a HANA Large Instance unit deployed in another region (for example, US East). This restriction was independent of the use of Azure network peering across regions or cross-connecting the ExpressRoute circuits that connect HANA Large Instance units to virtual networks. См. рисунок в разделе "Использование единиц крупных экземпляров HANA в нескольких регионах". This restriction, which came with the deployed architecture, prohibited the immediate use of HANA System Replication as disaster recovery functionality. For recent changes, look up the section 'Use HANA Large Instance units in multiple regions'. 

* SAP HANA on Azure (Large Instances) units have an assigned IP address from the server IP pool address range that you submitted when requesting the HANA Large Instance deployment. Дополнительные сведения см. в статье [Инфраструктура и возможности подключения SAP HANA в Azure (крупные экземпляры)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). This IP address is accessible through the Azure subscriptions and circuit  that connects Azure virtual networks to HANA Large Instances. IP-адрес, назначенный из этого диапазона IP-адресов пула серверов, напрямую присваивается аппаратной единице. NAT больше *не* используется, как это имело место в первых развертываниях этого решения. 

### <a name="direct-routing-to-hana-large-instances"></a>Direct Routing to HANA Large Instances

By default, the transitive routing does not work in these scenarios:

* Between HANA Large Instance units and an on-premises deployment.

* Between HANA Large Instance routing that are deployed in two different regions.

There are three ways to enable transitive routing in those scenarios:

- Обратный прокси-сервер для маршрутизации входных и выходных данных. For example, F5 BIG-IP, NGINX with Traffic Manager deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises as a virtual firewall/traffic routing solution.
- Используйте [правила IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) на виртуальной машине Linux для маршрутизации между локальным расположением и единицами HANA (крупные экземпляры) или между единицами HANA (крупные экземпляры) в разных регионах. The VM running IPTables needs to be deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises. The VM needs to be sized accordingly, so, that the network throughput of the VM is sufficient for the expected network traffic. For details on VM network bandwidth, check the article [Sizes of Linux virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) would be another solution to enable direct traffic between on-premises and HANA Large instance units. 

All the traffic of these solutions would be routed through an Azure virtual network and as such the traffic could be additionally restricted by the soft appliances used or by Azure Network Security Groups, so, that certain IP addresses or IP address ranges from on-premises could be blocked or explicitly allowed accessing HANA Large Instances. 

> [!NOTE]  
> Имейте в виду, что внедрение и поддержка пользовательских решений с использованием сетевых устройств сторонних производителей или утилиты IPTables не предоставляется корпорацией Майкрософт. Поддержка должна предоставляться поставщиком используемого компонента или интегратором. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft introduced a new functionality called [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach can be used for HANA Large Instances in two scenarios:

- Enable direct access from on-premises to your HANA Large Instance units deployed in different regions
- Enable direct communication between your HANA Large Instance units deployed in different regions


##### <a name="direct-access-from-on-premises"></a>Direct Access from on-premises
In the Azure regions where Global Reach is offered, you can request enabling the Global Reach functionality for your ExpressRoute circuit that connects your on-premises network to the Azure virtual network that connects to your HANA Large Instance units as well. There are some cost implications for the on-premises side of your ExpressRoute circuit. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). There are no additional costs for you related to the circuit that connects the HANA Large Instance unit(s) to Azure. 

> [!IMPORTANT]  
> In case of using Global Reach for enabling direct access between your HANA Large Instance units and on-premises assets, the network data and control flow is **not routed through Azure virtual networks**, but directly between the Microsoft enterprise exchange routers. As a result any NSG or ASG rules, or any type of firewall, NVA, or proxy you deployed in an Azure virtual network, are not getting touched. **If you use ExpressRoute Global Reach to enable direct access from on-premises to HANA Large instance units restrictions and permissions to access HANA large Instance units need to be defined in firewalls on the on-premises side** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connecting HANA Large Instances in different Azure regions
In the same way, as ExpressRoute Global Reach can be used for connecting on-premises to HANA Large Instance units, it can be used to connect tow HANA Large Instance tenants that are deployed for you in two different regions. The isolation is the ExpressRoute circuits that your HANA Large Instance tenants are using to connect to Azure in both regions. There are no additional charges for connecting two HANA Large Instance tenants that are deployed in two different regions. 

> [!IMPORTANT]  
> The data flow and control flow of the network traffic between the different HANA Large instance tenants will not be routed through azure networks. As a result you can't use Azure functionality or NVAs to enforce communication restrictions between your two HANA Large Instances tenants. 

For more details on how to get ExpressRoute Global Reach enabled, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Подключение крупных экземпляров HANA к Интернету
Крупные экземпляры HANA *не* подключены к Интернету напрямую. Это ограничивает ваши возможности, например зарегистрировать образ ОС напрямую с использованием поставщика ОС. Вам может потребоваться поработать с локальным сервером средства для управления подписками SUSE Linux Enterprise Server или Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Шифрование данных между виртуальными машинами и крупными экземплярами HANA
Данные, передаваемые между виртуальными машинами и крупными экземплярами HANA, не шифруются. Однако исключительно для обмена между СУБД HANA и приложениями на основе JDBC/ODBC вы можете включить шифрование трафика. Дополнительные сведения см. в этой [документации по SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Использование единиц крупных экземпляров HANA в нескольких регионах

To realize disaster recovery set ups, you need to have SHANA Large Instance units in multiple Azure regions. Even with using Azure [Global Vnet Peering], the transitive routing by default is not working between HANA Large Instance tenants in two different regions. However, Global Reach opens up the communication path between the HANA Large Instance units you have provisioned in two different regions. This usage scenario of ExpressRoute Global Reach enables:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes


![Виртуальные сети, подключенные к стекам крупных экземпляров Azure в разных регионах Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

The figure shows how the different virtual networks in both regions are connected to two different ExpressRoute circuits that are used to connect to SAP HANA on Azure (Large Instances) in both Azure regions (grey lines). Reason for this two cross connections is to protect from an outage of the MSEEs on either side. The communication flow between the two virtual networks in the two Azure regions is supposed to be handled over the [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) of the two virtual networks in the two different regions (blue dotted line). The thick red line describes the ExpressRoute Global Reach connection, which allows the HANA Large Instance units of your tenants in two different regions to communicate with each other. 

> [!IMPORTANT] 
> Если используется несколько каналов ExpressRoute, чтобы обеспечить правильную маршрутизацию трафика, следует использовать атрибут AS PATH и настройки локального предпочтения BGP.

**Дальнейшие действия**
- Дополнительные сведения см. в статье [Архитектура хранилища SAP HANA (крупные экземпляры)](hana-storage-architecture.md).

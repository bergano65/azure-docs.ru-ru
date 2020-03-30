---
title: Виртуальная сеть для служб Azure
titlesuffix: Azure Virtual Network
description: Сведения о преимуществах развертывания ресурсов в виртуальной сети. Ресурсы в виртуальной сети могут взаимодействовать друг с другом и локальным ресурсом, не передавая трафик через Интернет.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279667"
---
# <a name="virtual-network-integration-for-azure-services"></a>Интеграция виртуальной сети для служб Azure

Интеграция служб Azure с виртуальной сетью Azure обеспечивает закрытый доступ к службе из виртуальных машин или вычислительных ресурсов в виртуальной сети.
Службы Azure можно интегрировать с виртуальной сетью такими способами:
- Развертывание выделенных экземпляров службы в виртуальной сети. В этом случае закрытый доступ к этим службам сможет осуществляться в виртуальной сети и из локальных сетей.
- Использование [Private Link](../private-link/private-link-overview.md) для доступа в частном порядке к определенному экземпляру службы из вашей виртуальной сети и из закрытых сетей.

Вы также можете получить доступ к службе с помощью общедоступных конечных точек, расширив виртуальную сеть до службы через [конечные точки службы.](virtual-network-service-endpoints-overview.md) Конечные точки обслуживания позволяют обеспечить безопасность ресурсов службы в виртуальной сети.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Развертывание служб Azure в виртуальных сетях

При развертывании выделенных служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.

![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN-подключение типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [канал ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Экземпляры обслуживания в виртуальной сети, как правило, полностью управляются службой Azure. Это включает в себя мониторинг работоспособности ресурсов и масштабирование с нагрузкой.
- Экземпляры служб развертываются в подсети в виртуальной сети. Входящий и исходящий сетевой доступ к подсети должен быть открыт через [группы сетевой безопасности,](security-overview.md#network-security-groups)в рамках руководства, предоставляемого службой.
- Некоторые службы также налагают ограничения на подсеть, в которой они развернуты, ограничивая применение политик, маршрутов или комбинируя водной связи и ресурсы обслуживания в одной подсети. Проверьте с каждой службой на конкретные ограничения, как они могут меняться с течением времени. Примерами таких сервисов являются Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- При необходимости для служб можно использовать [делегированную подсеть](virtual-network-manage-subnet.md#add-a-subnet), чтобы явно указать, что в этой подсети может размещаться определенная служба. Делегируя, службы получают явные разрешения на создание ресурсов, специфиченных для обслуживания, в делегированной подсети.
- Смотрите пример ответа REST API в [виртуальной сети с делегированной подсетью.](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet) Полный перечень услуг, использующих модель делегированной подсети, можно получить через API [доступных делегаций.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Службы, которые можно развернуть в виртуальной сети

|Категория|Служба| Выделенный1 Подсеть
|-|-|-|
| Службы вычислений | Виртуальные машины: [Windows](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Наборы виртуального масштаба машины](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Облачная служба](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая).<br/> [Пакетная служба Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| нет <br/> нет <br/> нет <br/> No2
| Сеть | [Шлюз приложений — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Брандмауэр Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Сетевые виртуальные устройства](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Да <br/> Да <br/> Да <br/> нет
|Данные|[РедисКаче](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Управляемая инстанциями базы данных Azure S'L](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Да <br/> Да <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Лазурные databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No2 <br/> No2 <br/> 
| идентификации | [Службы активных доменов Azure](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |нет <br/>
| Контейнеры | [Служба Лазурного Кубернета (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Экземпляр контейнеров Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Обработчик службы контейнеров Azure](https://github.com/Azure/acs-engine) с [подключаемым модулем](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI для виртуальной сети Azure|No2<br/> Да <br/><br/> нет
| Интернет | [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Среда обслуживания приложений](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/> Да
| Размещенные* | [Выделенное устройство HSM Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/>
| | |

1 'Выделенные' подразумевает, что в этой подсети могут быть развернуты только конкретные ресурсы обслуживания, которые не могут быть объединены с клиентами VM/VMSSs <br/> 2 В качестве наилучшей практики рекомендуется иметь эти услуги в специальной подсети, но не является обязательным требованием, предъявляемым службой.

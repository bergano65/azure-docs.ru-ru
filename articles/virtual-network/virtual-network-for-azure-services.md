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
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409509"
---
# <a name="virtual-network-integration-for-azure-services"></a>Интеграция виртуальной сети для служб Azure

Интеграция служб Azure с виртуальной сетью Azure обеспечивает закрытый доступ к службе из виртуальных машин или вычислительных ресурсов в виртуальной сети.
Службы Azure можно интегрировать с виртуальной сетью такими способами:
- Развертывание выделенных экземпляров службы в виртуальной сети. В этом случае закрытый доступ к этим службам сможет осуществляться в виртуальной сети и из локальных сетей.
- Расширение виртуальной сети за счет добавления конечной точки службы. Конечные точки позволяют защитить ресурсы отдельной службы в виртуальной сети.

Чтобы интегрировать несколько служб Azure с виртуальной сетью, можно использовать одну или объединить несколько указанных выше схем. Например, вы можете развернуть в виртуальной сети службу HDInsight и защитить учетную запись хранения в подсети HDInsight с помощью конечных точек службы.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Развертывание служб Azure в виртуальных сетях

При развертывании выделенных служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.

![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN-подключение типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [канал ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Экземпляры службы в виртуальной сети обычно полностью управляются службой Azure. Сюда входят наблюдения за исправностью ресурсов и масштабирования с нагрузкой.
- Экземпляры служб развертываются в подсети в виртуальной сети. Входящий и исходящий сетевой доступ для подсети должен быть открыт посредством [группы безопасности сети](security-overview.md#network-security-groups), согласно рекомендациям, предоставляемых службой.
- Определенные службы также накладывают ограничения на подсети, к которой они развернуты, и ограничения применения политики, маршруты или сочетания виртуальных машин и ресурсов службы в той же подсети. Проверьте с каждой службой, на определенные ограничения, как они могут изменяться со временем. Примерами таких служб являются экземпляры контейнеров Azure для файлов NetApp Azure, выделенного аппаратного модуля безопасности, службы приложений. 
- При необходимости для служб можно использовать [делегированную подсеть](virtual-network-manage-subnet.md#add-a-subnet), чтобы явно указать, что в этой подсети может размещаться определенная служба. Делегирование, службы получают явные разрешения для создания конкретной службы ресурсов в делегированного подсети.
- Пример ответа REST API см. в разделе на [виртуальной сети с подсетью делегированного](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). Полный список служб, использующих модель делегированной подсети можно получить с помощью [доступных делегирования](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Службы, которые можно развернуть в виртуальной сети

|Category|Service| Dedicated¹ подсети
|-|-|-|
| Среда выполнения приложений | Виртуальные машины: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Облачная служба](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая)<br/> [Пакетная служба Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Нет <br/> Нет <br/> Нет <br/> No²
| Сеть | [Шлюз приложений — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Брандмауэр Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Виртуальные сетевые модули](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Да <br/> Да <br/> Да <br/> Нет
|Данные|[Кэш Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Да <br/> Да <br/> 
|Аналитика | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Удостоверение | [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Нет <br/>
| Контейнеры | [Служба Azure Kubernetes (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Экземпляр контейнера Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Обработчик службы контейнеров Azure](https://github.com/Azure/acs-engine) с [подключаемым модулем](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI для виртуальной сети Azure|No²<br/> Да <br/><br/> Нет
| Интернет | [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Среда службы приложений](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/> Да
| Размещенные | [Выделенное устройство HSM Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/>
| | |

¹ 'Dedicated' означает, что только службы к определенным ресурсам можно развернуть в этой подсети и нельзя использовать вместе с клиентом виртуальной Машины/VMSSs <br/> ² рекомендуется, но не обязательное требование, наложенные службой.


## <a name="service-endpoints-for-azure-services"></a>Конечные точки служб Azure

Некоторые службы Azure невозможно развернуть в виртуальной сети. Вы можете предоставить доступ к некоторым ресурсам службы только определенным подсетям виртуальной сети, включив конечные точки служб для виртуальной сети.  Дополнительные сведения о конечных точках служб для виртуальной сети и службах, для которых можно создать конечные точки, см. [здесь](virtual-network-service-endpoints-overview.md).

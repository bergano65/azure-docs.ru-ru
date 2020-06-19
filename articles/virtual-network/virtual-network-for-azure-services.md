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
ms.openlocfilehash: cd953eacbef5c157d529e1865c2225d656806da9
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780477"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Развертывание выделенных служб Azure в виртуальных сетях

При развертывании выделенных служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.

![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN-подключение типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [канал ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Экземпляры службы в виртуальной сети обычно полностью управляются службой Azure. Сюда входит отслеживание работоспособности ресурсов и масштабирование с учетом нагрузки.
- Экземпляры служб развертываются в подсети в виртуальной сети. Входящий и исходящий доступ к подсети должен быть открыт посредством [групп безопасности сети](security-overview.md#network-security-groups) согласно рекомендациям для служб.
- Некоторые службы также накладывают ограничения на подсеть, в которой они развернуты, ограничивая применение политик, маршруты или сочетания виртуальных машин и ресурсов служб в одной подсети. Проверяйте ограничения по каждой службе, так как со временем они могут меняться. Примерами таких служб являются Azure NetApp Files, Выделенное устройство HSM, Экземпляры контейнеров Azure, Служба приложений. 
- При необходимости для служб можно использовать [делегированную подсеть](virtual-network-manage-subnet.md#add-a-subnet), чтобы явно указать, что в этой подсети может размещаться определенная служба. Делегирование подсети прямо разрешает службам создавать в подсети необходимые для них ресурсы.
- См. пример ответа REST API в [виртуальной сети с делегированной подсетью](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Полный список служб, использующих модель делегированной подсети, можно получить с помощью API [Доступные делегирования](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list).

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Службы, которые можно развернуть в виртуальной сети

|Категория|Служба| Выделенная<sup>1</sup>sup>1</sup> подсеть
|-|-|-|
| Службы вычислений | Виртуальные машины: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Облачная служба](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая)<br/> [Пакетная служба Azure](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Нет <br/> Нет <br/> Нет <br/> Нет<sup>2</sup>sup>2</sup>
| Сеть | [Шлюз приложений — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Брандмауэр Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Виртуальные сетевые модули](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Да <br/> Да <br/> Да <br/> Нет
|Данные|[Кэш Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Управляемый экземпляр Базы данных SQL Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Да <br/> Да <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Нет<sup>2</sup>sup>2</su<sup>2</sup>> <br/> Нет<sup>2</sup> <br/> 
| Идентификация | [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Нет <br/>
| Контейнеры | [Служба Azure Kubernetes (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Экземпляр контейнера Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Обработчик службы контейнеров Azure](https://github.com/Azure/acs-engine) с [подключаемым модулем](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI для виртуальной сети Azure<br/>[Функции Azure](../azure-functions/functions-networking-options.md#virtual-network-integration) |Нет<sup>2</sup>sup>2</sup><br/> Да <br/><br/> Нет <br/> Да
| Интернет | [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Веб-приложения](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Среда службы приложений](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/> Да <br/> Да
| Размещенные* | [Выделенное устройство HSM Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Да <br/> Да <br/>
| | |

<sup>1</sup> "Выделенная" означает, что в этой подсети можно развертывать только ресурсы, относящиеся к службе, и не может использоваться вместе с виртуальными машинами или Масштабируемыми наборами виртуальных машин клиента. <br/> 
<sup>2</sup> Рекомендуется организовать работу этих служб в выделенной подсети, но это не обязательное требование, накладываемое службой.

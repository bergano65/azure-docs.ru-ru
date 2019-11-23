---
title: Конечные точки служб для виртуальной сети Azure
titlesuffix: Azure Virtual Network
description: Узнайте, как включить прямой доступ к ресурсам Azure из виртуальной сети с помощью конечных точек служб.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378694"
---
# <a name="virtual-network-service-endpoints"></a>Конечные точки служб для виртуальной сети

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. Конечные точки позволяют защищать критически важные ресурсы служб Azure в пределах отдельных виртуальных сетей. Трафик, поступающий из виртуальной сети в службу Azure, всегда остается в магистральной сети Microsoft Azure.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**Общедоступная версия**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**Общедоступная предварительная версия**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

Самые актуальные уведомления доступны на странице [обновлений виртуальной сети Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Основные преимущества

Конечные точки служб обеспечивают следующие преимущества.

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Конечные точки служб обеспечивают оптимальную маршрутизацию трафика Azure. 

  Конечные точки всегда направляют трафик служб непосредственно из виртуальной сети в службу в магистральной сети Microsoft Azure. Так как трафик не покидает пределы магистральной сети, это позволяет и дальше выполнять аудит и мониторинг исходящего интернет-трафика из виртуальных сетей при помощи принудительного туннелирования без воздействия на трафик служб. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Простота настройки и управления.** Больше не нужны зарезервированные общедоступные IP-адреса в виртуальных сетях для защиты ресурсов Azure с помощью брандмауэра IP-адресов. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>Ограничения

- Функция доступна только для виртуальных сетей, развернутых посредством модели развертывания с помощью Azure Resource Manager.
- Конечные точки включаются в подсетях, которые настроены в виртуальных сетях Azure. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- В SQL Azure конечная точка службы применяется только к трафику службы Azure в пределах региона виртуальной сети. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. Дополнительные сведения см. в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Эти утверждения используются для проверки подлинности виртуальной сети в учетной записи ADLS 1-го поколения и для предоставления доступа. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- Конечная точка службы для виртуальной сети предоставляет службе Azure удостоверение виртуальной сети. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- В настоящее время трафик служб Azure из виртуальной сети использует общедоступные IP-адреса в качестве исходных IP-адресов. При помощи конечных точек служб трафик в качестве исходных IP-адресов использует частные адреса виртуальной сети при обращении к службе Azure из виртуальной сети. Это переключение позволяет осуществлять доступ к службам без необходимости в зарезервированных общедоступных IP-адресах, которые используются в брандмауэрах.

   >[!NOTE]
   > При использовании конечных точек службы исходные IP-адреса виртуальных машин в подсети для трафика служб переключаются с общедоступных адресов IPv4 на частные. Существующие правила брандмауэра службы Azure, использующие общедоступные IP-адреса Azure, перестанут работать после этого переключения. Перед настройкой конечных точек службы убедитесь, что правила брандмауэра службы Azure поддерживают переключение. При настройке конечных точек службы также может наблюдаться временное прерывание трафика служб из этой подсети. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. Чтобы разрешить трафик из локальной среды, необходимо также разрешить общедоступные IP-адреса (обычно это NAT) из локальных каналов или каналов ExpressRoute. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Защита служб Azure в виртуальных сетях](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Настройка

- Configure service endpoints on a subnet in a virtual network. Конечные точки работают с любым типом вычислительных экземпляров, выполняющихся в этой подсети.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- Для Базы данных SQL Azure виртуальные сети должны относиться к тому же региону, что и ресурс службы Azure. При использовании учетных записей хранения Azure GRS и RA-GRS основная учетная запись должна принадлежать тому же региону, что и виртуальная сеть. For all other services, you can secure Azure service resources to virtual networks in any region. 
- Виртуальная сеть, в которой настраивается конечная точка, может относиться к той же подписке, что и ресурс службы Azure, либо же к другой подписке. Дополнительные сведения о разрешениях, которые требуются для настройки конечных точек и защиты служб Azure, см. в разделе [Подготовка](#provisioning).
- Вы можете защитить новые или существующие ресурсы в виртуальных сетях с помощью конечных точек служб для поддерживаемых служб.

### <a name="considerations"></a>Рекомендации

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. Во время этого переключения все открытые TCP-подключения к службе закрываются. Убедитесь, что при включении или отключении конечной точки службы в подсети не выполняются критически важные задачи. Также удостоверьтесь, что приложения могут автоматически подключаться к службам Azure после этого переключения IP-адресов.

  Коммутатор IP-адресов влияет только на трафик служб из виртуальной сети. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. Если для служб Azure установлены правила брандмауэра с использованием общедоступных IP-адресов Azure, то после переключения на частные адреса виртуальной сети эти правила перестают работать.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Группы безопасности сети (NSG) с конечными точками служб:
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. Дополнительные сведения см. в статье о [тегах служб Azure для групп NSG](security-overview.md#service-tags). 

### <a name="scenarios"></a>Сценарии

- **Пиринговые виртуальные сети, связанные виртуальные сети или несколько виртуальных сетей**. Чтобы обеспечить защиту служб Azure в нескольких подсетях в виртуальной сети или в нескольких виртуальных сетях, вы можете включить конечные точки служб для каждой из этих подсетей независимо друг от друга и защитить ресурсы служб Azure во всех этих подсетях.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. Это позволяет применять конечные точки служб к подсети, в которой развертывается виртуальное сетевое устройство, и обеспечить защиту ресурса службы Azure только в этой подсети. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. Дополнительные сведения см. в разделе [Deploy highly available network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha) (Развертывание высокодоступных сетевых устройств).
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. Вы можете защитить ресурсы служб Azure в подсетях [управляемой службы](virtual-network-for-azure-services.md), настроив конечную точку службы в подсети управляемой службы.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Ведение журнала и устранение неполадок

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- Проверка исходного IP-адреса любого запроса к службе при ее диагностике. Во всех новых запросах с использованием конечных точек служб исходный IP-адрес отображается как частный IP-адрес виртуальной сети, который назначен клиенту, выполняющему запрос из виртуальной сети. Без конечной точки этот адрес является общедоступным IP-адресом Azure.
- Просмотр действующих маршрутов любого сетевого интерфейса в подсети. Маршрут к службе:
  - отображает более точный маршрут по умолчанию с учетом префиксов адресов каждой службы;
  - использует значение *VirtualNetworkServiceEndpoint* параметра nextHopType;
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> Маршрут конечной точки службы переопределяет маршруты BGP или UDR при совпадении префиксов адресов для службы Azure. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Подготовка

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Виртуальные сети и ресурсы служб Azure могут находиться в одной или разных подписках. Если они находятся в разных подписках, ресурсы должны быть размещены в одном клиенте Active Directory (AD). 

## <a name="pricing-and-limits"></a>Цены и ограничения

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. Политики конечных точек служб предоставляют возможность детального контроля доступа трафика из виртуальной сети к службам Azure. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Часто задаваемые вопросы

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Дальнейшие действия

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Политики конечных точек служб для виртуальной сети (предварительная версия)](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).
- [Шаблон Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)


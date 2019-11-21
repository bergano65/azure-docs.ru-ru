---
title: Plan a virtual network for Azure HDInsight
description: Learn how to plan an Azure Virtual Network deployment to connect HDInsight to other cloud resources, or resources in your datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: d337d026e89d2383e25498288ba11a9c60f77b39
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228994"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Plan a virtual network for Azure HDInsight

This article provides background information on using [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) with Azure HDInsight. It also discusses design and implementation decisions that must be made before you can implement a virtual network for your HDInsight cluster. Once the planning phase is finished, you can proceed to [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md). For more information on HDInsight management IP addresses that are needed to properly configure network security groups and user-defined routes, see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).

Виртуальная сеть Azure обеспечивает реализацию следующих сценариев:

* Подключение к HDInsight непосредственно из локальной сети.
* Подключение к хранилищам данных HDInsight в виртуальной сети Azure.
* Прямой доступ к службам [Apache Hadoop](https://hadoop.apache.org/), недоступным из Интернета. Например, возможность напрямую работать с API [Apache Kafka](https://kafka.apache.org/) или использовать API Java для [Apache HBase](https://hbase.apache.org/).

> [!IMPORTANT]
> Creating an HDInsight cluster in a VNET will create several networking resources, such as NICs and load balancers. Do **not** delete these networking resources, as they are needed for your cluster to function correctly with the VNET.
>
> After Feb 28, 2019, the networking resources (such as NICs, LBs, etc) for NEW HDInsight clusters created in a VNET will be provisioned in the same HDInsight cluster resource group. Previously, these resources were provisioned in the VNET resource group. There is no change to the current running clusters and those clusters created without a VNET.

## <a name="planning"></a>Планирование

Ниже перечислены вопросы, на которые необходимо ответить, если планируется установка HDInsight в виртуальной сети.

* Необходимо ли установить HDInsight в существующей виртуальной сети? Или вы создаете новую сеть?

    При использовании существующей виртуальной сети может потребоваться изменить параметры конфигурации сети, прежде чем устанавливать HDInsight. Дополнительные сведения см. в разделе [Добавление HDInsight в существующую виртуальную сеть](#existingvnet).

* Необходимо ли подключить виртуальную сеть, содержащую HDInsight, к другой виртуальной сети или к локальной сети?

    Для удобства работы с ресурсами в сетях может потребоваться создать пользовательский DNS-сервер и настроить переадресацию DNS. Дополнительные сведения см. в разделе [Подключение нескольких сетей](#multinet).

* Необходимо ли ограничить/перенаправить входящий или исходящий трафик в HDInsight?

    Для HDInsight требуется неограниченная связь с определенными IP-адресами в центре обработки данных Azure. Существует несколько портов, которые следует разрешить в брандмауэрах для взаимодействия с клиентами. Дополнительные сведения см. в разделе [Управление сетевым трафиком](#networktraffic).

## <a id="existingvnet"></a>Добавление HDInsight в существующую виртуальную сеть

Следуйте инструкциям в этом разделе, чтобы узнать, как добавить новый кластер HDInsight в существующую виртуальную сеть Azure.

> [!NOTE]  
> В виртуальную сеть невозможно добавить существующий кластер HDInsight.

1. Какая модель используется для развертывания виртуальной сети: классическая модель или развертывание с помощью диспетчера ресурсов?

    Для HDInsight 3.4 и более поздней версии требуется виртуальная сеть диспетчера ресурсов. Для работы более ранних версий HDInsight требовалась классическая виртуальная сеть.

    Если существующая сеть является классической виртуальной сетью, необходимо создать виртуальную сеть диспетчера ресурсов и затем соединить их друг с другом. [Подключение виртуальных сетей из разных моделей развертывания с помощью портала](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    После присоединения экземпляр HDInsight, который установлен в сети диспетчера ресурсов, может взаимодействовать с ресурсами в классической сети.

2. Применяются ли группы безопасности сети, определяемые пользователем маршруты или виртуальные сетевые устройства для ограничения входящего или исходящего трафика виртуальной сети?

    Поскольку HDInsight является управляемой службой, ей требуется неограниченный доступ к нескольким IP-адресам в центре обработки данных Azure. Чтобы разрешить взаимодействие с этими IP-адресами, обновите существующие группы безопасности сети и определяемые пользователем маршруты.
    
    В состав HDInsight входит несколько служб, которые используют различные порты. Не блокируйте трафик на этих портах. Список портов, трафик через которые следует разрешить на межсетевых экранах виртуального устройства, см. в разделе "Безопасность".
    
    Чтобы найти существующую конфигурацию безопасности, используйте следующие команды Azure PowerShell или Azure CLI:

    * Группы безопасности сети

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Дополнительные сведения см. в документе, посвященном [устранению неполадок с группами безопасности сети](../virtual-network/diagnose-network-traffic-filter-problem.md).

        > [!IMPORTANT]  
        > Правила группы безопасности сети применяются в порядке, основанном на приоритете правила. Применяется первое правило, которое соответствует шаблону трафика; другие правила не применяются к этому трафику. Правила применяются в следующем порядке: от правила с максимальными разрешениями к правилу с минимальными разрешениями. Дополнительные сведения см. в документе [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).

    * Пользовательские маршруты

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Дополнительные сведения см. в документе, посвященном [устранению неполадок с маршрутами](../virtual-network/diagnose-network-routing-problem.md).

3. Создайте кластер HDInsight и выберите виртуальную сеть Azure во время настройки. Следуйте инструкциям в следующих документах, чтобы понять процесс создания кластера:

    * [Создание кластера HDInsight с использованием портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Создание кластера HDInsight с использованием Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Создание кластеров HDInsight с помощью интерфейса командной строки Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Создание кластеров Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Добавление кластера HDInsight в виртуальную сеть — это необязательный шаг настройки. При настройке кластера не забудьте выбрать виртуальную сеть.

## <a id="multinet"></a>Подключение нескольких сетей

Самая сложная задача в конфигурации с несколькими сетями — это разрешение имен между сетями.

Azure предоставляет разрешение имен для служб Azure, установленных в виртуальной сети. Эта встроенная функция разрешения имен позволяет HDInsight подключаться к следующим ресурсам с помощью полного доменного имени (FQDN):

* любые ресурсы, доступные в Интернете; например, microsoft.com, windowsupdate.com;

* любой ресурс, который находится в той же виртуальной сети Azure, с помощью __внутреннего DNS-имени__ ресурса; For example, when using the default name resolution, the following are examples of internal DNS names assigned to HDInsight worker nodes:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net;
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net.

    Оба этих узла могут взаимодействовать напрямую друг с другом и с другими узлами в HDInsight, используя внутренние DNS-имена.

При использовании разрешения имен по умолчанию HDInsight __запрещено__ разрешать имена ресурсов в сетях, которые присоединены к виртуальной сети. Например, подключение локальной сети к виртуальной сети является распространенной практикой. Если используется лишь разрешение имен по умолчанию, HDInsight не может обращаться к ресурсам локальной сети по имени. Это также относится и к ресурсам: ресурсы в локальной сети не могут обращаться к ресурсам в виртуальной сети по имени.

> [!WARNING]  
> Перед созданием кластера HDInsight необходимо создать пользовательский DNS-сервер и настроить виртуальную сеть на использование этого сервера.

Чтобы включить разрешение имен между виртуальной сетью и ресурсами в присоединенных сетях, выполните указанные ниже действия.

1. Создайте пользовательский DNS-сервер в виртуальной сети Azure, где планируется установить HDInsight.

2. Настройте виртуальную сеть для использования с пользовательским DNS-сервером.

3. Найдите назначенный Azure DNS-суффикс для виртуальной сети. Это значение аналогично `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Сведения о поиске DNS-суффиксов см. в разделе [Пример: пользовательский DNS-сервер](hdinsight-create-virtual-network.md#example-dns).

4. Настройте перенаправление между DNS-серверами. Конфигурация зависит от типа удаленной сети.

   * Если удаленная сеть является локальной, настройте DNS следующим образом:
        
     * __Пользовательский DNS-сервер__ (в виртуальной сети):

         * Перенаправляйте запросы DNS-суффиксов виртуальной сети на рекурсивный сопоставитель Azure (168.63.129.16). Azure обрабатывает запросы к ресурсам в виртуальной сети

         * Остальные запросы перенаправляйте на локальный DNS-сервер. Локальный DNS-сервер обрабатывает все остальные запросы на разрешение имен, включая запросы к интернет-ресурсам, таким как microsoft.com.

     * __Локальный DNS-сервер__: перенаправляйте запросы DNS-суффиксов виртуальной сети на пользовательский DNS-сервер. Последний затем перенаправляет их на рекурсивный сопоставитель Azure.

       Такая конфигурация пересылает запросы полных доменных имен с DNS-суффиксом для виртуальных сетей на пользовательский DNS-сервер. Все остальные запросы (даже для общедоступных интернет-адресов) обрабатываются локальным DNS-сервером.

   * Если удаленная сеть является еще одной виртуальной сетью Azure, настройте DNS следующим образом:

     * __Пользовательский DNS-сервер__ (в каждой виртуальной сети):

         * Запросы DNS-суффиксов виртуальных сетей перенаправляются на пользовательские DNS-серверы. DNS-сервер в каждой виртуальной сети должен разрешать ресурсы в своей сети.

         * Все остальные запросы перенаправляются на рекурсивный сопоставитель Azure. Рекурсивный сопоставитель отвечает за разрешение локальных и интернет-ресурсов.

       DNS-сервер для каждой сети перенаправляет запросы на другой сервер на основе DNS-суффикса. Все остальные запросы разрешаются рекурсивным сопоставителем Azure.

     Пример каждой конфигурации см. в разделе [Пример: пользовательский DNS-сервер](hdinsight-create-virtual-network.md#example-dns).

Дополнительные сведения см. в документе [Разрешение имен для виртуальных машин и экземпляров ролей](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="directly-connect-to-apache-hadoop-services"></a>Прямое соединение со службами Apache Hadoop

Вы можете подключиться к кластеру по адресу `https://CLUSTERNAME.azurehdinsight.net`. Этот адрес использует общедоступный IP-адрес, который может быть недоступен, если вы использовали NSG для ограничения входящего трафика из Интернета. Кроме того, при развертывании кластера в виртуальной сети доступ к нему можно получить с помощью частной конечной точки `https://CLUSTERNAME-int.azurehdinsight.net`. Эта конечная точка разрешается в частный IP-адрес виртуальной сети для доступа к кластеру.

Для подключения к Apache Ambari и другим веб-страницам через виртуальную сеть сделайте следующее:

1. Чтобы найти внутренние полные доменные имена (FQDN) узлов кластера HDInsight, используйте один из следующих методов:

    Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    В возвращенном списке узлов найдите полные доменные имена (FQDN) головных узлов и используйте эти имена для подключения к Ambari и другим веб-службам. Например, воспользуйтесь `http://<headnode-fqdn>:8080` для доступа к Ambari.

    > [!IMPORTANT]  
    > Некоторые службы, размещенные на головных узлах, одновременно активны только на одном узле. Если при попытке доступа к службе на одном головном узле появляется сообщение об ошибке 404, переключитесь на другой головной узел.

2. Чтобы определить узел и порт, через которые доступна служба, обратитесь к документу [Порты, используемые службами Hadoop в HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a>Управление сетевым трафиком

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniques for controlling inbound and outbound traffic to HDInsight clusters

Для управления сетевым трафиком в виртуальных сетях Azure можно использовать следующие методы:

* **Группы безопасности сети** (NSG) позволяют фильтровать входящий и исходящий трафик в сети. Дополнительные сведения см. в документе [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).

* **Network virtual appliances** (NVA) can be used with outbound traffic only. NVAs replicate the functionality of devices such as firewalls and routers. Дополнительные сведения см. в документе [Сетевые устройства](https://azure.microsoft.com/solutions/network-appliances).

As a managed service, HDInsight requires unrestricted access to the HDInsight health and management services both for incoming and outgoing traffic from the VNET. When using NSGs, you must ensure that these services can still communicate with HDInsight cluster.

![Diagram of HDInsight entities created in Azure custom VNET](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight with network security groups

If you plan on using **network security groups** to control network traffic, perform the following actions before installing HDInsight:

1. Определите регион Azure, который планируется использовать для HDInsight.

2. Identify the service tags required by HDInsight for your region. For more information, see [Network security group (NSG) service tags for Azure HDInsight](hdinsight-service-tags.md).

3. Create or modify the network security groups for the subnet that you plan to install HDInsight into.

    * __Группы безопасности сети__: разрешите __входящий__ трафик через порт __443__ с диапазона IP-адресов. This will ensure that HDInsight management services can reach the cluster from outside the virtual network.

For more information on network security groups, see the [overview of network security groups](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlling outbound traffic from HDInsight clusters

For more information on controlling outbound traffic from HDInsight clusters, see [Configure outbound network traffic restriction for Azure HDInsight clusters](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Forced tunneling to on-premises

Принудительное туннелирование — это определяемая пользователем конфигурация маршрутизации, где весь трафик из подсети принудительно направляется в определенную сеть или расположение, например в локальную сеть. HDInsight does __not__ support forced tunneling of traffic to on-premises networks. 

## <a id="hdinsight-ip"></a> Требуемые IP-адреса

If you use network security groups or user-defined routes to control traffic, please see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a> Требуемые порты

Если вы планируете использовать **брандмауэр** и получить доступ к кластеру за пределами определенных портов, вам следует разрешить трафик на этих портах, необходимых для вашего сценария. По умолчанию специальные списки разрешенных портов не требуются, если трафик управления Azure, описанный в предыдущем разделе, может достигать кластера на порте 443.

См. [список портов, используемых службами Hadoop в HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Дополнительные сведения о правилах межсетевого экрана для виртуальных модулей см. в документе [Сценарий использования виртуальных устройств](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="load-balancing"></a>Балансировка нагрузки

When you create an HDInsight cluster, a load balancer is created as well. The type of this load balancer is at the [basic SKU level](../load-balancer/load-balancer-overview.md#skus) which has certain constraints. One of these constraints is that if you have two virtual networks in different regions, you cannot connect to basic load balancers. See [virtual networks FAQ: constraints on global vnet peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), for more information.

## <a name="next-steps"></a>Дальнейшие действия

* For code samples and examples of creating Azure Virtual Networks, see [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md).
* Полный пример настройки HDInsight для подключения к локальной сети см. в статье [Подключение HDInsight к локальной сети](./connect-on-premises-network.md).
* For configuring Apache HBase clusters in Azure virtual networks, see [Create Apache HBase clusters on HDInsight in Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* См. инструкции по [настройке георепликации кластера Apache HBase в виртуальных сетях Azure](hbase/apache-hbase-replication.md).
* Дополнительные сведения о виртуальных сетях Azure см. в статье [Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md).
* Дополнительные сведения о группах безопасности сети см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).
* Дополнительные сведения о пользовательских маршрутах см. в статье [User-defined routes and IP forwarding](../virtual-network/virtual-networks-udr-overview.md) (Определяемые пользователем маршруты и IP-пересылка).

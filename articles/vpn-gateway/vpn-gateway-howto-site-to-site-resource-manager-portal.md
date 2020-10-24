---
title: 'Подключение локальной сети к виртуальной сети Azure: VPN типа "сеть — сеть": портал'
description: Создайте подключение VPN-шлюза типа "сеть — сеть" из локальной сети к виртуальной сети Azure через общедоступный Интернет с помощью портала.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: 069add9a7e5dd6b19810b8b36a4fca49818bfb02
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479632"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Создание подключения типа "сеть — сеть" на портале Azure

В этой статье показано, как с помощью портала Azure создать подключение типа "сеть — сеть" с использованием VPN-шлюза между вашей локальной сетью к виртуальной. Приведенные в этой статье инструкции относятся к модели развертывания с помощью Resource Manager. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Портал Azure (классический)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

Подключение VPN-шлюза типа "сеть — сеть" используется для подключения между локальной сетью и виртуальной сетью Azure через туннель VPN по протоколу IPsec/IKE (IKEv1 или IKEv2). Для этого типа подключения требуется локальное VPN-устройство, которому назначен внешний общедоступный IP-адрес. Дополнительные сведения о VPN-шлюзах см. в [этой статье](vpn-gateway-about-vpngateways.md).

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Схема подключения типа &quot;сеть — сеть&quot; через VPN-шлюз":::

## <a name="prerequisites"></a>Предварительные требования

Перед началом настройки убедитесь, что удовлетворены следующие требования:

* Учетная запись Azure с активной подпиской. Если у вас ее нет, [создайте ее бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Убедитесь, что у вас есть совместимое VPN–устройство и пользователь, который может настроить его. Дополнительные сведения о совместимых устройствах VPN и их настройке см. в [этой статье](vpn-gateway-about-vpn-devices.md).
* Убедитесь, что у вас есть общедоступный IPv4–адрес для вашего VPN–устройства.
* Если вы не знаете диапазоны IP-адресов в своей конфигурации локальной сети, найдите того, кто сможет предоставить вам нужную информацию. При создании этой конфигурации необходимо указать префиксы диапазона IP-адресов, которые Azure будет направлять к локальному расположению. Ни одна из подсетей локальной сети не может перекрывать виртуальные подсети, к которым вы хотите подключиться.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Создание виртуальной сети

Создайте виртуальную сеть (VNet), используя следующие значения:

* **Группа ресурсов:** TestRG1
* **Имя:** VNet1
* **Регион:** (США) Восточная часть США
* **Пространство IPv4-адресов:** 10.1.0.0/16
* **Имя подсети:** Интерфейса
* **Адресное пространство подсети:** 10.1.0.0/24.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Создание VPN-шлюза

На этом шаге вы создадите шлюз для своей виртуальной сети. Создание шлюза часто занимает 45 минут и более, в зависимости от выбранного SKU шлюза.

### <a name="about-the-gateway-subnet"></a>Сведения о подсети шлюза

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Создание шлюза

Создайте VPN-шлюз, используя следующие значения:

* **Имя:** VNet1GW
* **Регион:** Восточная часть США
* **Тип шлюза:** ЧЕРЕЗ
* **Тип VPN:** На основе маршрутов
* **Номер SKU:** VpnGw1
* **Поколение:** Generation1
* **Виртуальная сеть:** VNet1
* **Диапазон адресов подсети шлюза:** 10.1.255.0/27
* **Общедоступный IP-адрес:** Создать новый
* **Имя общедоступного IP-адреса:** VNet1GWpip
* **Включить режим "активный — активный":** Доступ
* **Настройка BGP:** Доступ

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Создание локального сетевого шлюза

Шлюз локальной сети — это конкретный объект, который представляет локальное расположение (сайт) для целей маршрутизации. Присвойте сайту имя, по которому Azure может обращаться к этому сайту, а затем укажите IP-адрес локального VPN-устройства, к которому вы подключитесь. Вы можете также указать префиксы IP-адресов, которые будут направляться через VPN-шлюз к VPN-устройству. Указываемые префиксы адресов расположены в локальной сети. Если вы внесли изменения в локальной сети или вам нужно изменить общедоступный IP-адрес для VPN-устройства, значения можно легко обновить позже.

Создайте шлюз локальной сети, используя следующие значения:

* **Имя:** Сайту site1
* **Группа ресурсов:** TestRG1
* **Расположение:** Восточная часть США

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Настройка VPN-устройства

Для подключения типа "сеть — сеть" к локальной сети требуется VPN-устройство. На этом этапе мы настроим VPN-устройство. При настройке VPN-устройства вам потребуются следующие значения:

* Общий ключ. Это тот же общий ключ, который указывается при создании VPN-подключения "сеть — сеть". В наших примерах мы используем простые общие ключи. Для практического использования рекомендуется создавать более сложные ключи.
* Общедоступный IP-адрес шлюза виртуальной сети. Общедоступный IP-адрес можно просмотреть с помощью портала Azure, PowerShell или CLI. Чтобы найти общедоступный IP-адрес VPN-шлюза с помощью портал Azure, перейдите к разделу **шлюзы виртуальной сети**, а затем выберите имя шлюза.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Создание VPN-подключения

Создайте VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и локальным VPN-устройством.

Создайте подключение, используя следующие значения:

* **Имя шлюза локальной сети:** Сайту site1
* **Имя подключения:** VNet1toSite1
* **Общий ключ:** в этом примере мы используем abc123. Но можно использовать любой ключ, совместимый с оборудованием VPN. Важно, чтобы значения совпадали на обеих сторонах подключения.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Проверка VPN-подключения

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Подключение к виртуальной машине

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Как сбросить VPN-шлюз

Сброс настроек VPN-шлюза Azure полезен при потере распределенного VPN-подключения в одном или нескольких VPN-туннелях типа "сеть-сеть". В этой ситуации все локальные VPN-устройства работают правильно, но не могут взаимодействовать с VPN-шлюзами Azure через туннели IPsec. Пошаговые инструкции см. в статье [Сброс VPN-шлюза](reset-gateway.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Как изменить SKU шлюза (изменение размера шлюза)

Пошаговые инструкции для изменения SKU шлюза см. в статье [Работа со SKU шлюза виртуальной сети (старые версии SKU)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Как добавить дополнительное подключение к VPN-шлюзу

Вы можете добавлять дополнительные подключения при условии, что ни один из диапазонов адресов не перекрывается между подключениями.

1. Чтобы добавить дополнительное подключение, перейдите к VPN-шлюзу и выберите **подключения** , чтобы открыть страницу подключения.
1. Выберите **+ Добавить** , чтобы добавить подключение. Задайте соответствующий тип соединения: "виртуальная сеть — виртуальная сеть" (при подключении к другому шлюзу виртуальной сети) или "сеть — сеть".
1. Если вы используете подключение "сеть — сеть", но еще не создали шлюз для локальной сети, к которой подключаетесь, создайте этот шлюз.
1. Укажите общий ключ, который требуется использовать, а затем нажмите кнопку **ОК** , чтобы создать подключение.

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).
* Сведения о принудительном туннелировании см. в статье [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](vpn-gateway-forced-tunneling-rm.md).
* Сведения о высокодоступных подключениях в режиме "активный — активный" см. в статье [Настройка высокодоступных подключений: распределенных и между виртуальными сетями](vpn-gateway-highlyavailable.md).
* Сведения об ограничении сетевого трафика к ресурсам в виртуальной сети см. в статье [Безопасность сети](../virtual-network/security-overview.md).
* Сведения о том, как Azure маршрутизирует трафик между средой Azure, локальной средой и интернет-ресурсами, см. в статье [Маршрутизация трафика в виртуальной сети](../virtual-network/virtual-networks-udr-overview.md).
* Сведения о создании VPN-подключения "сеть — сеть" с помощью шаблона Azure Resource Manager см. в разделе [Create a Site-to-Site VPN Connection](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) (Создание VPN-подключения "сеть — сеть").
* Сведения о создании VPN-подключения между виртуальными сетями с помощью шаблона Azure Resource Manager см. в статье [развертывание георепликации HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

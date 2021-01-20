---
title: Создание VPN между Azure и AWS с помощью управляемых решений
description: Как создать VPN-подключение между Azure и AWS с помощью управляемых решений вместо виртуальных машин или устройств.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: cherylmc
ms.openlocfilehash: c1bc263ca67a7d05dbb0d40bb07ba1ae43c2db5c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605645"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Создание VPN-подключения между Azure и AWS с помощью управляемых решений

Вы можете установить подключение между Azure и AWS с помощью управляемых решений. Ранее было необходимо использовать устройство или виртуальную машину, действующую в качестве респондента. Теперь вы можете подключить виртуальный частный шлюз AWS к VPN-шлюзу Azure напрямую, не беспокоясь об управлении ресурсами IaaS, такими как виртуальные машины. Эта статья поможет вам создать VPN-подключение между Azure и AWS, используя только управляемые решения.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Схема архитектуры":::

## <a name="configure-azure"></a>Настройка Azure

### <a name="configure-a-virtual-network"></a>Настройка виртуальной сети

Настройте виртуальную сеть. Инструкции см. в [кратком руководстве по виртуальной сети](../virtual-network/quick-create-portal.md).

В этой статье используются следующие примеры значений:

* **Группа ресурсов:** RG-Azure-AWS
* **Регион**: Восточная часть США
* **Имя виртуальной сети:** виртуальная сеть — Azure
* **Пространство IPv4-адресов:** 172.10.0.0/16
* **Имя подсети:** Subnet-01
* **Диапазон адресов подсети:** 172.10.1.0/24.

### <a name="create-a-vpn-gateway"></a>Создание VPN-шлюза

Создайте VPN-шлюз для виртуальной сети. Инструкции см. [в разделе Учебник. Создание VPN-шлюза и управление им](tutorial-create-gateway-portal.md).

В этой статье используются следующие примеры значений и параметров.

* **Тип шлюза**: Виртуальная частная сеть
* **Тип VPN**: на основе маршрута
* **SKU**: VpnGw1
* **Поколение:** Поколение 1
* **Виртуальная сеть:** Это должна быть виртуальная сеть, для которой нужно создать шлюз.
* **Диапазон адресов подсети шлюза:** 172.10.0.0/27
* **Общедоступный IP-адрес**. Создание
* **Имя общедоступного IP-адреса:** PIP-VPN-Azure-AWS
* **Включить режим "активный — активный":** Включен
* **Настройка BGP:** Включен

Пример:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Сводка по шлюзу виртуальной сети":::

## <a name="configure-aws"></a>Настройка AWS

1. Создайте виртуальное частное облако (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Создание сведений об VPC":::

1. Создайте подсеть внутри VPC (виртуальная сеть).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Создание подсети":::

1. Создайте пользовательский шлюз, указывающий на общедоступный IP-адрес VPN-шлюза Azure. **Шлюз клиента** — это ресурс AWS, содержащий сведения о AWS о устройстве шлюза клиента, который в данном случае является VPN-шлюзом Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Создание пользовательского шлюза":::

1. Создайте виртуальный частный шлюз.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Создание виртуального частного шлюза":::

1. Подключите виртуальный частный шлюз к VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Подключение ВПГ к VPC":::

1. Выберите VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Attach":::

1. Создайте VPN-подключение типа "сеть — сеть".

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Создание VPN-подключения":::

1. Задайте для параметра маршрутизации значение **static** и укажите префикс подсети Azure-01 **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Настройка статического маршрута":::

1. После заполнения параметров **Создайте** соединение.

1. Скачайте файл конфигурации. Чтобы скачать правильную конфигурацию, измените поставщик, платформу и программное обеспечение на **универсальный**, так как Azure не является допустимым вариантом.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Скачать конфигурацию":::

1. Файл конфигурации содержит общий ключ и общедоступный IP-адрес для каждого из двух туннелей IPsec, созданных AWS.

   **Туннель 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Туннель 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Конфигурация туннеля 1":::

   **Туннель 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Туннель 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Конфигурация туннеля 2":::

1. После создания туннелей вы увидите примерно такой пример.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Сведения о VPN-подключении AWS":::

## <a name="create-local-network-gateway"></a>Создание шлюза локальной сети

В Azure шлюз локальной сети — это ресурс Azure, который обычно представляет локальное расположение. Он заполняется сведениями, используемыми для подключения к локальному VPN-устройству. Однако в этой конфигурации создается шлюз локальной сети, который заполняется сведениями о подключении к виртуальному частному шлюзу AWS. Дополнительные сведения о шлюзах локальной сети Azure см. в статье [параметры VPN-шлюза Azure](vpn-gateway-about-vpn-gateway-settings.md#lng).

Создайте шлюз локальной сети в Azure. Инструкции см. [в разделе Создание шлюза локальной сети](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Укажите следующие значения:

* **Имя:** В этом примере мы используем LNG-Azure-AWS.
* **Конечная точка:** IP-адрес
* **IP-адрес:** Общедоступный IP-адрес из виртуального частного шлюза AWS и префикс CIDR для VPC. Общедоступный IP-адрес можно найти в скачанном ранее файле конфигурации.

AWS создает два туннеля IPsec для обеспечения высокой доступности. В следующем примере показан общедоступный IP-адрес из туннеля IPsec #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Шлюз локальной сети":::

## <a name="create-vpn-connection"></a>Создание VPN-подключения

В этом разделе вы создадите VPN-подключение между шлюзом виртуальной сети Azure и шлюзом AWS.

1. Создайте подключение Azure. Инструкции по созданию подключения см. в разделе [Создание VPN-подключения](tutorial-site-to-site-portal.md#CreateConnection).

   В следующем примере общий ключ был получен из скачанного ранее файла конфигурации. В этом примере используются значения для туннеля IPsec #1, созданные AWS, и описаны в файле конфигурации.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Объект подключения Azure":::

1. Просмотр подключения. Через несколько минут соединение будет установлено.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Рабочее подключение":::

1. Убедитесь, что #1 туннеля IPsec AWS **работает.**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Убедитесь, что туннель AWS включен":::

1. Измените таблицу маршрутов, связанную с VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Изменение маршрута":::

1. Добавьте маршрут в подсеть Azure. Этот маршрут будет проходить через VPC-шлюз.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Сохранение конфигурации маршрута":::

## <a name="configure-second-connection"></a>Настройка второго подключения

В этом разделе вы создадите второе подключение, чтобы обеспечить высокий уровень доступности.

1. Создайте другой шлюз локальной сети, который указывает на общедоступный IP-адрес туннеля IPsec #2 на AWS. Это резервный шлюз.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Создание локального сетевого шлюза":::

1. Создайте второе VPN-подключение из Azure к AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Создание резервного подключения к шлюзу локальной сети":::

1. Просмотр подключений VPN-шлюза Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Состояние подключения к Azure":::

1. Просмотр подключений AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Состояние подключения AWS":::

Теперь подключения установлены.

## <a name="test-connections"></a>Проверка подключения

1. Добавьте Шлюз Интернета в VPC по адресу AWS. Шлюз Интернета — это логическое подключение между VPN Amazon и Интернетом. Этот ресурс позволяет подключать тестовую виртуальную машину из общедоступного IP-адреса AWS через Интернет. Этот ресурс не требуется для VPN-подключения. Мы используем его только для тестирования.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Создание шлюза Интернета":::

1. Выберите **подключить к VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Подключение шлюза Интернета к VPC":::

1. Выберите VPC-образ и **Подключите интернет-шлюз**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Подключение шлюза":::

1. Создайте маршрут, чтобы разрешить подключения к **0.0.0.0/0** (Интернет) через шлюз Интернета.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Настройка маршрутизации через шлюз":::

1. В Azure маршрут создается автоматически. Чтобы проверить маршрут на виртуальной машине Azure, выберите **Виртуальная машина > сети > сетевой интерфейс > действующие маршруты**. Вы увидите 2 маршрута, 1 маршрут на одно подключение.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Проверка действующих маршрутов":::

1. На виртуальной машине Linux в Azure среда выглядит так же, как в следующем примере.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Обзор Azure с виртуальной машины Linux":::

1. На виртуальной машине Linux в AWS среда выглядит так же, как в следующем примере.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Обзор AWS с виртуальной машины Linux":::

1. Проверьте подключение виртуальной машины Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Проверка связи из Azure":::

1. Проверьте подключение виртуальной машины AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Проверка связи из AWS":::

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о поддержке AWS для IKEv2 см. в [статье AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).

---
title: Используйте шлюз приложений Azure с виртуальными машинами VMware
description: Описывает, как использовать шлюз приложения Azure для управления входящим веб-трафиком для веб-серверов, работающих в виртуальных машинах VMware, выигрывает среду CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015462"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Используйте azure Application Gateway с виртуальными машинами VMware в среде Облачного частного облака

Вы можете использовать Azure Application Gateway для управления входящим веб-трафиком для веб-серверов, работающих в виртуальных машинах VMware в среде CloudSimple Private Cloud.

Используя Azure Application Gateway в публично-частном гибридном развертывании, вы можете управлять веб-трафиком для приложений, обеспечивать безопасный фронт-энд и разгружать SSL-обработку для своих служб, работающих в среде VMware. Azure Application Gateway маршруты входящего веб-трафика для бэкэнд бассейн экземпляров, проживающих в средах VMware в соответствии с настроенными правилами и зондов работоспособности.

Это решение Azure Application Gateway требует от вас:

* Создать подписку Azure.
* Создайте и назначайте виртуальную сеть Azure и подсеть в виртуальной сети.
* Создавайте и настраивайте правила NSG и направляйте vNet с помощью ExpressRoute в свое облачное частное облако.
* Создайте & нанастройки вашего частного облака.
* Создайте & наверстывайте шлюз приложений Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Сценарий развертывания прикладных приложений Azure

В этом случае шлюз приложения Azure работает в виртуальной сети Azure. Виртуальная сеть подключена к частному облаку по схеме ExpressRoute. Все подсети в private Cloud доступны ip-адресу из виртуальных сетевых подсетей.

![Балансизатор нагрузки Azure в виртуальной сети Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Как развернуть решение

Процесс развертывания состоит из следующих задач:

1. [Проверить, что предпосылки выполнены](#1-verify-prerequisites)
2. [Подключение виртуального соединения Azure к частному облаку](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Развертывание шлюза приложения Azure](#3-deploy-an-azure-application-gateway)
4. [Создание и настройка пула Web Server VM в частном облаке](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Проверить предпосылки

Убедитесь, что эти предпосылки выполнены:

* Уже создан менеджер ресурсов Azure и виртуальная сеть.
* В виртуальной сети Azure уже создана специальная подсеть (для шлюза приложений).
* CloudSimple Private Cloud уже создан.
* В виртуальной сети и поднетами в частном облаке нет никакого IP-конфликта.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Подключите виртуальную сеть Azure к частному облаку

Чтобы подключить виртуальную сеть Azure к частному облаку, следуйте этому процессу.

1. [На портале CloudSimple скопируйте информацию о пиринге ExpressRoute.](virtual-network-connection.md)

2. [Назначьте виртуальный сетевой шлюз для виртуальной сети Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Свяжите свою виртуальную сеть с схемой CloudSimple ExpressRoute.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

4. [Используйте скопированную вами информацию для связи виртуальной сети с схемой ExpressRoute.](virtual-network-connection.md)

## <a name="3-deploy-an-azure-application-gateway"></a>3. Развертывание шлюза приложения Azure

Подробные инструкции для этого доступны в [создании шлюза приложения с правилами маршрутизации на основе маршрутов с помощью портала Azure.](../application-gateway/create-url-route-portal.md) Вот краткое изложение необходимых шагов:

1. Создайте виртуальную сеть в группе подписки и ресурсов.
2. Создайте подсеть (которая будет использоваться в качестве выделенной подсети) в вашей виртуальной сети.
3. Создайте стандартный шлюз приложений (по желанию включить WAF): С домашней страницы портала Azure щелкните**шлюз** **приложений для сетей** >  **ресурсов** > с верхней левой стороны страницы. Выберите стандартный SKU и размер и предоставьте информацию о подписке Azure, группе ресурсов и местоположении. При необходимости создайте новый общедоступный IP-адрес для этого шлюза приложения и предоставьте подробную информацию о виртуальной сети и выделенной подсети для шлюза приложения.
4. Добавьте бэкэнд-пул с виртуальными машинами и добавьте его в шлюз приложения.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Создание и настройка пула VM-сервера веб-сервера в частном облаке

В vCenter создавайте VMs с ОС и веб-сервером по вашему выбору (например, Windows/IIS или Linux/Apache). Выберите поднет/VLAN, предназначенный для веб-уровня в вашем частном облаке. Убедитесь, что по крайней мере один vNIC веб-сервера VMs находится на веб-уровне подсети.

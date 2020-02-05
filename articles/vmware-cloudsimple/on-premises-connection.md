---
title: Решения Azure VMware (AVS) — локальное подключение с помощью ExpressRoute
description: В этой статье описывается, как запросить локальное подключение с помощью ExpressRoute из сети с регионом AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019627"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Подключение из локальной среды к AVS с помощью ExpressRoute

Если у вас уже есть подключение Azure ExpressRoute из внешнего расположения (например, из локальной среды) в Azure, вы можете подключить его к среде AVS. Это можно сделать с помощью функции Azure, которая позволяет двум каналам ExpressRoute подключаться друг к другу. Этот метод устанавливает защищенную, частную, высокоскоростную и низкую задержку подключение между двумя средами.

[![подключение к локальной системе ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Перед началом работы

Для установления подключения Global Reach из локальной среды требуется блок сетевых адресов **/29** . Адресное пространство/29 используется для транзитной сети между каналами ExpressRoute. Транзитная сеть не должна перекрываться ни с одной из виртуальных сетей Azure, локальных сетей или с частными облачными сетями AVS.

## <a name="prerequisites"></a>Технические условия

* Перед установкой подключения между сетями и частными облаками AVS необходимо создать канал Azure ExpressRoute.
* Пользователь должен иметь права доступа для создания ключей авторизации в канале ExpressRoute.

## <a name="scenarios"></a>Сценарии

Подключение локальной сети к частной облачной сети AVS позволяет использовать частное облако AVS различными способами, включая следующие сценарии.

* Получите доступ к сети частного облака AVS без создания VPN-подключения типа "сеть — сеть".
* Используйте локальную Active Directory в качестве источника удостоверений в частном облаке AVS.
* Перенесите виртуальные машины, работающие локально, в частное облако AVS.
* Используйте частное облако AVS в качестве части решения для аварийного восстановления.
* Использование локальных ресурсов на виртуальных машинах рабочей нагрузки частного облака AVS.

## <a name="connecting-expressroute-circuits"></a>Подключение каналов ExpressRoute

Чтобы установить подключение ExpressRoute, необходимо создать авторизацию в канале ExpressRoute и предоставить сведения об авторизации в AVS.


### <a name="create-expressroute-authorization"></a>Создание авторизации ExpressRoute

1. Войдите на портал Azure.

2. В верхней строке поиска найдите **канал expressroute** и щелкните **каналы Expressroute** в разделе **службы**.
    [![каналов ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Выберите канал ExpressRoute, который планируется подключить к сети AVS.

4. На странице ExpressRoute щелкните **авторизации**, введите имя авторизации и нажмите кнопку **сохранить**.
    [![авторизации канала ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Скопируйте идентификатор ресурса и ключ авторизации, щелкнув значок копирования. Вставьте идентификатор и ключ в текстовый файл.
    [Копия авторизации канала ExpressRoute ![](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Идентификатор ресурса** должен быть скопирован из пользовательского интерфейса и должен быть в формате ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` при предоставлении поддержки.

6. Отправьте билет с <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">поддержкой</a> создаваемого подключения.
    * Тип проблемы: **Техническая**
    * Подписка: **Подписка, в которой развернута служба AVS**
    * Служба: **решения VMware (AVS)**
    * Тип проблемы: **запрос на обслуживание**
    * Подтип проблемы: **Создание подключения ExpressRoute к локальной среде**
    * Укажите идентификатор ресурса и ключ авторизации, которые были скопированы и сохранены в области сведений.
    * Укажите/29 адресного пространства сети для транзитной сети.
    * Вы отправляете маршрут по умолчанию через ExpressRoute?
    * Должен ли трафик частного облака AVS использовать маршрут по умолчанию, отправленный через ExpressRoute?

    > [!IMPORTANT]
    > Отправка маршрута по умолчанию позволяет отправлять весь Интернет-трафик из частного облака AVS с помощью локального подключения к Интернету. Чтобы отключить маршрут по умолчанию, настроенный в частном облаке AVS, и использовать локальный маршрут по умолчанию для локального подключения, укажите сведения в запрос в службу поддержки.

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о сетевых подключениях Azure](cloudsimple-azure-network-connection.md)  

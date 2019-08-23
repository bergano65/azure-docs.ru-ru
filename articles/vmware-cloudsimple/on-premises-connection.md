---
title: Решение Azure VMware по Клаудсимпле — локальное подключение с помощью ExpressRoute
description: В этой статье описывается, как запросить локальное подключение с помощью ExpressRoute из сети Клаудсимпле Region.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee359b76072da3caee9ae1f5fab3d0fc28d25c0e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972687"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Подключение из локальной среды к Клаудсимпле с помощью ExpressRoute

Если у вас уже есть подключение Azure ExpressRoute из внешнего расположения (например, из локальной среды) в Azure, вы можете подключить его к среде Клаудсимпле. Это можно сделать с помощью функции Azure, которая позволяет двум каналам ExpressRoute подключаться друг к другу. Этот метод устанавливает защищенную, частную, высокоскоростную и низкую задержку подключение между двумя средами.

[![Подключение к локальной системе ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="prerequisites"></a>Предварительные требования

* Перед установкой подключения между сетями и частными облаками Клаудсимпле необходимо создать канал ExpressRoute Azure.
* Пользователь должен иметь права доступа для создания ключей авторизации в канале ExpressRoute.

## <a name="scenarios"></a>Сценарии

Подключение локальной сети к сети частного облака позволяет использовать частное облако различными способами, включая следующие:

* Получите доступ к сети частного облака без создания VPN-подключения типа "сеть — сеть".
* Используйте локальные Active Directory в качестве источника удостоверений в частном облаке.
* Перенесите виртуальные машины, работающие локально, в частное облако.
* Используйте частное облако в качестве части решения для аварийного восстановления.
* Использование локальных ресурсов на виртуальных машинах рабочей нагрузки частного облака.

## <a name="connecting-expressroute-circuits"></a>Подключение каналов ExpressRoute

Чтобы установить подключение ExpressRoute, необходимо создать авторизацию в канале ExpressRoute и предоставить сведения об авторизации для Клаудсимпле.

### <a name="create-expressroute-authorization"></a>Создание авторизации ExpressRoute

1. Войдите на портал Azure.

2. В верхней строке поиска найдите **канал expressroute** и щелкните **каналы Expressroute** в разделе **службы**.
    [![Каналы ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Выберите канал ExpressRoute, который планируется подключить к сети Клаудсимпле.

4. На странице ExpressRoute щелкните **авторизации**, введите имя авторизации и нажмите кнопку **сохранить**.
    [![Авторизация канала ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Скопируйте идентификатор ресурса и ключ авторизации, щелкнув значок копирования. Вставьте идентификатор и ключ в текстовый файл.
    [![Копия авторизации канала ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Идентификатор ресурса** должен быть скопирован из пользовательского интерфейса и должен быть в формате ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` , если он предоставляется для поддержки.

6. Отправьте билет с <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">поддержкой</a> создаваемого подключения.
    * Тип проблемы. **Технические требования**
    * Подписка: **Подписка, в которой развернута служба Клаудсимпле**
    * Служба: **Решение VMware по Клаудсимпле**
    * Тип проблемы: **Запрос на обслуживание**
    * Подтип проблемы: **Создание подключения ExpressRoute к локальной среде**
    * Укажите идентификатор ресурса и ключ авторизации, которые были скопированы и сохранены в области сведений.

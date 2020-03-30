---
title: Решение Azure VMware от CloudSimple - подключение на территории с помощью ExpressRoute
description: Описывает, как запросить предпосылок с помощью ExpressRoute из сети CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019627"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Подключайтесь от находины к CloudSimple с помощью ExpressRoute

Если у вас уже есть подключение Azure ExpressRoute от внешнего местоположения (например, локальных) к Azure, вы можете подключить его к среде CloudSimple. Это можно сделать с помощью функции Azure, которая позволяет двум схемам ExpressRoute соединяться друг с другом. Этот метод устанавливает безопасную, частную, высокую пропускную способность, связь с низкой задержкой между двумя средами.

[![Подключение на территории Экспрессрута - Глобальный охват](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Перед началом

Для создания подключения Global Reach из находины требуется сетевой адресный блок **/29.**  Адресное пространство /29 используется для транзитной сети между схемами ExpressRoute.  Транзитная сеть не должна пересекаться ни с одной из виртуальных сетей Azure, сетей-находящихся в ней или сетях CloudSimple Private Cloud.

## <a name="prerequisites"></a>Предварительные требования

* Схема Azure ExpressRoute необходима, прежде чем вы сможете установить связь между цепью и сетями CloudSimple Private Cloud.
* Пользователь обязан иметь привилегии для создания ключей авторизации на схеме ExpressRoute.

## <a name="scenarios"></a>Сценарии

Подключение вашей преднациональной сети к сети Private Cloud позволяет использовать частное облако различными способами, включая следующие сценарии:

* Получите доступ к сети Private Cloud без создания VPN-соединения от сайта.
* Используйте свой закрытый Active Directory в качестве источника идентификации в личном облаке.
* Имитируйте виртуальные машины, работающие на месте, в ваше частное облако.
* Используйте приватное облако как часть решения аварийного восстановления.
* Потребляйте ресурсы на закрытых объектах на внедорожных вычислениях Private Cloud.

## <a name="connecting-expressroute-circuits"></a>Подключение схем ExpressRoute

Чтобы установить соединение ExpressRoute, необходимо создать авторизацию на трассе ExpressRoute и предоставить информацию о авторизации в CloudSimple.


### <a name="create-expressroute-authorization"></a>Создание авторизации ExpressRoute

1. Войдите на портал Azure.

2. Из верхней панели поиска, поиск **для схемы ExpressRoute** и нажмите **ExpressRoute схемы** в **рамках услуг**.
    [![Схемы ЭкспрессРут](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Выберите схему ExpressRoute, которую вы собираетесь подключить к сети CloudSimple.

4. На странице ExpressRoute нажмите **«Авторизанция»,** введите имя для авторизации и нажмите **«Сохранить».**
    [![Авторизация экспресс-маршрута](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Копируйте идентификатор ресурса и ключ авторизации, нажав значок копии. Вставьте идентификатор и ключ в текстовый файл.
    [![Копия авторизации схемы ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Идентификатор ресурса** должен быть скопирован ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` из uI и должен быть в формате, когда вы предоставляете его для поддержки.

6. Подать билет с <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">поддержкой</a> для создания соединения.
    * Тип выпуска: **Технический**
    * Подписка: **Подписка, в которой развернут сервис CloudSimple**
    * Сервис: **Решение VMware от CloudSimple**
    * Тип проблемы: **Запрос на обслуживание**
    * Подтип проблемы: **Создание подключения ExpressRoute к наемным помещениям**
    * Предоставьте идентификатор ресурса и ключ авторизации, который вы скопировали и сохранили в панели деталей.
    * Предоставьте адресное пространство сети /29 для транзитной сети.
    * Отправляете маршрут по умолчанию через ExpressRoute?
    * Должен ли трафик Private Cloud использовать маршрут по умолчанию, отправленный через ExpressRoute?

    > [!IMPORTANT]
    > Отправка маршрута по умолчанию позволяет отправлять весь интернет-трафик из Private Cloud с помощью вашего собственного интернет-соединения.  Чтобы отключить маршрут по умолчанию, настроенный на приватное облако, и использовать маршрут по умолчанию на месте, предоставьте сведения в поддержке билета поддержки.

## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее о сетевых соединениях Azure](cloudsimple-azure-network-connection.md)  

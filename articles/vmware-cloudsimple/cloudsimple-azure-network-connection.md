---
title: Решение VMware от CloudSimple - сетевые соединения Azure
description: Подробнее о подключении виртуальной сети Azure к региональной сети CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025084"
---
# <a name="azure-network-connections-overview"></a>Обзор сетевых подключений Azure

При создании службы CloudSimple в регионе и создании узлов можно:

* Запросите схему Azure ExpressRoute и прикрепите ее к сети CloudSimple в этом регионе.
* Подключите свою сеть CloudSimple к виртуальной сети Azure или к своей предварительной сети с помощью Azure ExpressRoute.
* Обеспечить доступ к службам, работающим в подписке Azure или к вашей предварительной сети из среды Private Cloud.

Соединение ExpressRoute имеет высокую пропускную способность с низкой задержкой.

## <a name="benefits"></a>Преимущества

Сетевое подключение Azure позволяет:

* Используйте Azure в качестве резервной мишени для виртуальных машин в частном облаке.
* Развертывание серверов KMS в подписке Azure для шифрования хранилища данных Private Cloud vSAN.
* Используйте гибридные приложения, где веб-уровень приложения работает в общедоступном облаке, в то время как уровни приложений и баз данных работают в вашем частном облаке.

## <a name="azure-virtual-network-connection"></a>Виртуальное сетевое соединение Azure

Частные облака могут быть подключены к ресурсам Azure с помощью ExpressRoute.  Подключение ExpressRoute позволяет получить доступ к ресурсам, работающим в подписке Azure, из вашего частного облака.  Это соединение позволяет расширить сеть Private Cloud до виртуальной сети Azure.  Маршруты из сети CloudSimple будут обмениваться с виртуальной сетью Azure через BGP.  Если у вас есть виртуальная сеть пиринг настроен, все заглянул виртуальных сетей будут доступны из вашей сети CloudSimple.

![Подключение Azure ExpressRoute к виртуальной сети](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Подключение ExpressRoute к припредречной сети

Вы можете подключить существующую схему Azure ExpressRoute к региону CloudSimple. Функция ExpressRoute Global Reach используется для соединения двух цепей друг с другом.  Устанавливается связь между контурами и схемами CloudSimple ExpressRoute.  Это соединение позволяет расширить свои сети в закрытом помещении до сети Private Cloud. Маршруты из вашей сети CloudSimple будут обмениваться через BGP с вашей предварительной сетью.

![Подключение на территории Экспрессрута - Глобальный охват](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Подключение к припредтежной сети и виртуальной сети Azure

Подключение к предприимской сети и виртуальной сети Azure может сосуществовать из вашей сети CloudSimple.  Соединение использует BGP для обмена маршрутами между приложечной сетью, виртуальной сетью Azure и сетью CloudSimple.  При подключении сети CloudSimple к виртуальной сети Azure в присутствии подключения Global Reach виртуальные сетевые маршруты Azure будут видны в вашей предварительной сети.  Обмен маршрутами происходит в Azure между маршрутизаторами края.

![Подключение ExpressRoute с виртуальным сетевым соединением Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Важные сведения

Подключение к сети CloudSimple из сети и из виртуальной сети Azure позволяет обмениваться маршрутами между всеми сетями.

* Виртуальная сеть Azure будет видна как из предприимчехи, так и из сети CloudSimple.
* Если вы подключились к виртуальной сети Azure из предварительной сети, подключение к сети CloudSimple с помощью Global Reach позволит получить доступ к виртуальным сетям из сети CloudSimple.
* Адреса подсети **не должны** пересекаться между любыми из подключенных сетей.
* CloudSimple **не** будет рекламировать маршрут по умолчанию для подключений ExpressRoute
* Если ваш маршрутизатор рекламирует маршрут по умолчанию, трафик из сети CloudSimple и виртуальной сети Azure будет использовать рекламируемый маршрут по умолчанию.  В результате виртуальные машины в Azure не могут быть доступны с помощью общедоступных IP-адресов.

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение виртуальной сети Azure к CloudSimple с помощью ExpressRoute](virtual-network-connection.md)
* [Подключайтесь от находины к CloudSimple с помощью ExpressRoute](on-premises-connection.md)

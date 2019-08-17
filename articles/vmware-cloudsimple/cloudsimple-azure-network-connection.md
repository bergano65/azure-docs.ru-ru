---
title: Решение VMware по Клаудсимпле — сетевые подключения Azure
description: Узнайте, как подключить виртуальную сеть Azure к сети Клаудсимпле Region
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563168"
---
# <a name="azure-network-connections-overview"></a>Общие сведения о сетевых подключениях Azure

При создании службы Клаудсимпле в регионе она:

* Создает канал Azure ExpressRoute и прикрепляет его к службе в этом регионе.
* Подключает сеть Клаудсимпле Region к виртуальной сети Azure или локальной сети с помощью Azure ExpressRoute.
* Предоставляет доступ к службам, работающим в вашей подписке Azure или локальной сети, из среды частного облака.

Подключение ExpressRoute имеет высокую пропускную способность с низкой задержкой.

## <a name="benefits"></a>Преимущества

Сетевое подключение Azure позволяет:

* Используйте Azure в качестве целевого объекта резервного копирования для виртуальных машин в частном облаке.
* Разверните серверы службы KMS в подписке Azure, чтобы зашифровать хранилище данных частного облака vSAN.
* Используйте гибридные приложения, в которых веб-уровень приложения выполняется в общедоступном облаке, а уровни приложения и базы данных выполняются в частном облаке.

## <a name="azure-virtual-network-connection"></a>Подключение к виртуальной сети Azure

Частные облака можно подключить к ресурсам Azure с помощью ExpressRoute.  Подключение ExpressRoute позволяет получить доступ к ресурсам, выполняемым в вашей подписке Azure, из частного облака.  Это подключение позволяет расширить сеть частного облака на виртуальную сеть Azure.

[![Подключение Azure ExpressRoute к виртуальной сети](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Подключение ExpressRoute к локальной сети

Вы можете подключить существующий канал Azure ExpressRoute к региону Клаудсимпле. Функция ExpressRoute Global Reach используется для подключения двух цепей друг к другу.  Между локальными и Клаудсимпле ExpressRoute устанавливаются соединения.  Это подключение позволяет расширять локальные сети в сеть частного облака.

![Подключение к локальной системе ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Следующие шаги

* [Подключение виртуальной сети Azure к Клаудсимпле с помощью ExpressRoute](virtual-network-connection.md)
* [Подключение из локальной среды к Клаудсимпле с помощью ExpressRoute](on-premises-connection.md)

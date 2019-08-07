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
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812705"
---
# <a name="azure-network-connections-overview"></a>Общие сведения о сетевых подключениях Azure

При создании службы Клаудсимпле в регионе она:

* Создает канал Azure ExpressRoute и прикрепляет его к службе в этом регионе.
* Разрешает подключение из сети региона Клаудсимпле к виртуальной сети Azure или локальной сети с помощью Azure ExpressRoute.
* Предоставляет службы доступа, выполняющиеся в вашей подписке Azure или в локальной сети, из среды частного облака.

Соединение:

* Безопасность
* Закрытый
* Высокая пропускная способность
* Низкая задержка

## <a name="benefits"></a>Преимущества

Сетевое подключение Azure позволяет:

* Используйте Azure в качестве целевого объекта резервного копирования для виртуальных машин в частном облаке.
* Разверните серверы службы KMS в подписке Azure, чтобы зашифровать хранилище данных частного облака vSAN.
* Используйте гибридные приложения, в которых веб-уровень приложения выполняется в общедоступном облаке, а уровни приложения и базы данных выполняются в частном облаке.

## <a name="azure-virtual-network-connection"></a>Подключение к виртуальной сети Azure

Частные облака можно подключить к ресурсам Azure с помощью ExpressRoute.  Это подключение можно использовать для доступа к различным ресурсам, выполняемым в вашей подписке Azure, из частного облака.  Это подключение позволяет расширить сеть частного облака на виртуальную сеть Azure.

![Подключение Azure ExpressRoute к виртуальной сети](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Подключение ExpressRoute к локальной сети

Вы можете подключить существующий канал Azure ExpressRoute к региону Клаудсимпле. Функция ExpressRoute Global Reach используется для подключения двух цепей друг к другу.  Между локальными и Клаудсимпле ExpressRoute устанавливаются соединения.  Это подключение позволяет расширять локальные сети в сеть частного облака.

![Подключение к локальной системе ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Следующие шаги

* [Получение сведений о пиринга для подключения виртуальной сети Azure к Клаудсимпле](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Подключение из локальной среды к Клаудсимпле с помощью ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)

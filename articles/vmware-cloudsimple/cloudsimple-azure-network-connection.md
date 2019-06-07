---
title: Решение VMware, CloudSimple - Azure сетевые подключения
description: Дополнительные сведения о подключении виртуальной сети Azure к сети CloudSimple регион
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497642"
---
# <a name="azure-network-connections-overview"></a>Общие сведения о подключениях сети Azure

При создании службы CloudSimple в регионе, он:

* Создает канал Azure ExpressRoute и присоединяет его к службе в этом регионе
* Разрешает подключение из сети CloudSimple регионе для вашей виртуальной сети Azure или в локальной сети с помощью Azure ExpressRoute
* Предоставляет доступ служб, работающих в вашей подписке Azure или в локальной сети, в среде частного облака

Соединение установлено:

* Безопасность
* Private
* Высокая пропускная способность
* Низкая задержка

## <a name="benefits"></a>Преимущества

Подключение сети Azure позволяет:

* Использование Azure в качестве целевого объекта архивации для виртуальных машин на частным облаком.
* Развертывание серверов службы KMS в подписке Azure для шифрования в хранилище данных vSAN частного облака.
* Используйте гибридные приложения, где веб-уровень приложения запускается в общедоступном облаке во время выполнения приложения, и уровни базы данных выполняется в частным облаком.

## <a name="azure-virtual-network-connection"></a>Подключение виртуальной сети Azure

Частные облака можно подключить к вашим ресурсам Azure с помощью ExpressRoute.  Можно использовать это подключение к различным ресурсам, работающего в вашей подписке Azure с частным облаком.  Это подключение позволяет расширить вы сети частного облака для виртуальной сети Azure.

![Подключение Azure ExpressRoute для виртуальной сети](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Подключение ExpressRoute к локальной сети

Ваши существующие каналы Azure ExpressRoute можно подключить к региону CloudSimple. ExpressRoute глобальным доступом используются для подключения двух цепей друг с другом.  Подключение устанавливается между локальным и каналы CloudSimple ExpressRoute.  Это подключение позволяет переносить в локальной сети к сети частного облака.

![Локальное подключение ExpressRoute — глобальный доступ](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Получить информацию о пиринге для виртуальной сети Azure подключение CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Подключение из локальной к CloudSimple, с помощью ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)

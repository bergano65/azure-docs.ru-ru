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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577005"
---
# <a name="azure-network-connection-overview"></a>Общие сведения о подключении сети Azure

При создании службы CloudSimple в регионе, он:

* Создает канал Azure ExpressRoute и присоединяет его к службе в этом регионе
* Подключается к вашей виртуальной сети Azure или в локальной сети с помощью Azure ExpressRoute CloudSimple область сети
* Предоставляет доступ служб, работающих в вашей подписке Azure или в локальной сети, в среде частного облака

Это подключение является высокая пропускная способность с низкой задержкой.

## <a name="benefits"></a>Преимущества

Подключение сети Azure позволяет:

* Использование Azure в качестве целевого объекта архивации для виртуальных машин на частным облаком.
* Развертывание серверов службы KMS в подписке Azure для шифрования в хранилище данных vSAN частного облака.
* Используйте гибридные приложения, где веб-уровень приложения запускается в общедоступном облаке во время выполнения приложения, и уровни базы данных выполняется в частным облаком.

## <a name="expressroute-connection-to-on-premises-network"></a>Подключение ExpressRoute к локальной сети

Ваши существующие каналы Azure ExpressRoute можно подключить к региону CloudSimple. ExpressRoute глобальным доступом используются для подключения двух цепей друг с другом.  Подключение устанавливается между локальным и каналы CloudSimple ExpressRoute.

Этот метод устанавливает соединение между двумя средами, которые:

* Безопасность
* Private
* Высокая пропускная способность
* Низкая задержка

Чтобы создать подключение ExpressRoute к локальной сети, [обратитесь в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка подключения виртуальной сети](https://docs.azure.cloudsimple.com/virtual-network-connection)
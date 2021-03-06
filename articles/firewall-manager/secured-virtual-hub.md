---
title: Что такое защищенный виртуальный концентратор?
description: Дополнительные сведения о защищенных виртуальных концентраторах
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518441"
---
# <a name="what-is-a-secured-virtual-hub"></a>Что такое защищенный виртуальный концентратор?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Виртуальный концентратор — это виртуальная сеть, управляемая Майкрософт, которая обеспечивает подключение из других ресурсов. При создании виртуального концентратора из виртуальной глобальной сети в портал Azure в качестве его компонентов создаются виртуальная сеть и шлюзы виртуального концентратора (необязательно).

*Защищенный* виртуальный концентратор — это [виртуальный концентратор глобальной сети Azure](../virtual-wan/virtual-wan-about.md#resources) со связанными политиками безопасности и маршрутизации, настроенными диспетчером брандмауэра Azure. Используйте защищенные виртуальные концентраторы для простого создания промежуточных и транзитивных архитектур с собственными службами безопасности для управления трафиком и их защиты. 

Защищенный виртуальный концентратор можно использовать в качестве управляемой Центральной виртуальной сети без локального подключения. Она заменяет центральную виртуальную сеть, которая ранее требовалась для развертывания брандмауэра Azure. Так как защищенный виртуальный концентратор обеспечивает автоматическую маршрутизацию, нет необходимости настраивать собственный определяемые пользователем маршруты (определяемые пользователем маршруты) для маршрутизации трафика через брандмауэр.

Также можно использовать защищенные виртуальные концентраторы в рамках полной виртуальной глобальной сети. Эта архитектура обеспечивает защищенное, оптимизированное и автоматизированное подключение филиала к и через Azure. Вы можете выбрать службы для защиты и управления сетевым трафиком, включая брандмауэр Azure и других сторонних поставщиков безопасности как услуги (Секаас).

## <a name="create-a-secured-virtual-hub"></a>Создание защищенного виртуального концентратора

С помощью диспетчера брандмауэра в портал Azure можно создать новый защищенный виртуальный концентратор или преобразовать существующий виртуальный концентратор, созданный ранее с помощью виртуальной глобальной сети Azure.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о создании защищенного виртуального концентратора и его использовании для защиты и управления концентратором и периферийной сетью см. [в статье руководство. Защита облачной сети с помощью диспетчера брандмауэра Azure с использованием портал Azure](secure-cloud-network.md).
---
title: Azure Load Balancer конфигурация с плавающей IP-адресом
description: Общие сведения о Azure Load Balancer с плавающим IP-адресом
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: e15aab3ebfe82fa97f5716769b5ff9675b4b3dc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637441"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer конфигурация с плавающей IP-адресом

Load Balancer предоставляет указанные ниже возможности для приложений TCP и UDP.

## <a name="floating-ip"></a>Плавающий IP-адрес

В некоторых сценариях приложений использование одного порта несколькими экземплярами приложения на одной виртуальной машине во внутреннем пуле является предпочтительным или обязательным. К распространенным примерам повторного использования портов относятся виртуальные сетевые устройства, кластеризация для обеспечения высокой доступности и размещение нескольких конечных точек TLS без повторного шифрования. Если вы хотите, чтобы внутренний порт повторно использовался в нескольких правилах, то в определении правила необходимо активировать плавающий IP-адрес.

Согласно терминологии Azure, **плавающий IP-адрес** — это часть так называемого прямого ответа от сервера (DSR). DSR состоит из двух частей:

- топология потока;
- схема сопоставления IP-адресов.

На уровне платформы Azure Load Balancer всегда работает в топологии потока DSR независимо от того, активирован ли плавающий IP-адрес. Это означает, что исходящая часть потока всегда правильно перезаписывается в поток непосредственно обратно в источник.
Если плавающий IP-адрес не используется, для удобства применения Azure предоставляет традиционную схему сопоставления IP-адресов с балансировкой нагрузки (IP-адреса экземпляров виртуальных машин). Если включить плавающий IP-адрес, то схему сопоставления IP-адресов заменит интерфейсный IP-адрес подсистемы балансировки нагрузки для обеспечения дополнительной гибкости. Дополнительные сведения см. [здесь](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Ограничения

- Плавающий IP-адрес в настоящее время не поддерживается во вторичных IP-конфигурациях для сценариев балансировки нагрузки

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе с Load Balancer, см. [Create a public Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) (Создание общедоступного Load Balancer (цен. категория "Стандартный")).
- Ознакомьтесь со сведениями об [исходящих подключениях Azure Load Balancer](load-balancer-outbound-connections.md).
- [Дополнительные сведения об Azure Load Balancer](load-balancer-overview.md).
- Дополнительные сведения о [пробах работоспособности](load-balancer-custom-probe-overview.md).
- [Metrics and health diagnostics for Standard Load Balancer](load-balancer-standard-diagnostics.md) (Метрики и проверки работоспособности Load Balancer уровня "Стандартный")
- Узнайте больше о [группах безопасности сети](../virtual-network/security-overview.md).

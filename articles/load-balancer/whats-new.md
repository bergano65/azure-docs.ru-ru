---
title: Новые возможности Azure Load Balancer
description: Сведения о новых возможностях Azure Load Balancer, включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147482"
---
# <a name="whats-new-in-azure-load-balancer"></a>Новые возможности Azure Load Balancer

Azure Load Balancer регулярно обновляется. Будьте в курсе последних объявлений. В этой статье вы ознакомитесь:

- Последние выпуски.
- Известные проблемы
- Исправления ошибок
- со сведениями о нерекомендуемых функциях (если это применимо).

Кроме того, [здесь](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) вы найдете последние новости, касающиеся Azure Load Balancer, и сможете подписаться на канал RSS.

## <a name="new-features"></a>новые функции;

|Компонент  |Описание  |Дата добавления  |
|---------|---------|---------|
| Поддержка управления серверным пулом на основе IP-адресов (предварительная версия) | Azure Load Balancer поддерживает добавление и удаление ресурсов из серверного пула с помощью адресов IPv4 или IPv6. Это позволяет без труда управлять контейнерами, виртуальными машинами и масштабируемыми наборами виртуальных машин, связанными с Load Balancer. Кроме того, можно резервировать IP-адреса в составе серверного пула до создания связанных ресурсов. | Июль 2020 г. |
| Azure Load Balancer Insights с использованием Azure Monitor | Клиенты также могут использовать топологические карты для всех конфигураций и панелей мониторинга работоспособности Load Balancer, которые предоставляются с Azure Monitor для сетей, в своих службах Load Balancer цен. категории "Стандартный", предварительно бесплатно настроенных с метриками на портале Azure. [Начало работы и дополнительные сведения](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Июнь 2020 г. |
| Поддержка IPv6 для Azure Load Balancer (общедоступная версия) | Адреса IPv6 можно использовать в качестве внешнего интерфейса для ресурсов Azure Load Balancer. Узнайте, как [создать приложение с двойным стеком](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). |Апрель 2020 г.|
| Сброс TCP при истечении времени ожидания простоя (общедоступная версия)| Сброс TCP поможет добиться более предсказуемого поведения приложения. [Дополнительные сведения](load-balancer-tcp-reset.md)| Февраль 2020 г. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Что такое Azure Load Balancer?](load-balancer-overview.md), а также статье с [вопросами и ответами](load-balancer-faqs.md).
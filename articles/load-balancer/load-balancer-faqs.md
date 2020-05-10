---
title: Часто задаваемые вопросы — Azure Load Balancer
description: Ответы на часто задаваемые вопросы о Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005159"
---
# <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

## <a name="what-types-of-load-balancer-exist"></a>Какие типы Load Balancer существуют?
Внутренние подсистемы балансировки нагрузки, которые балансируют трафик в виртуальной сети и внешних подсистемах балансировки нагрузки, которые сбалансировать трафик между конечной точкой, подключенной к Интернету, и из нее Дополнительные сведения см. в разделе [типы Load Balancer](components.md#frontend-ip-configurations). 

Для обоих этих типов Azure предлагает базовый SKU и SKU уровня "Стандартный", которые имеют различные возможности для работы, производительности, безопасности и отслеживания работоспособности. Эти различия описаны в статье [Сравнение номеров SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Как выполнить обновление с базовой на Load Balancer (цен. категория "Стандартный")?
Инструкции по обновлению SKU Load Balancer см. в статье [обновление с уровня "базовый" до "Стандартный](upgrade-basic-standard.md) " для автоматизированного сценария.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Каковы различные варианты балансировки нагрузки в Azure?
Сведения о доступных службах балансировки нагрузки и рекомендации по их использованию см. в статье о [технологии подсистемы балансировки нагрузки](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) .

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Где можно найти шаблоны Load Balancer ARM?
См. [список шаблонов](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) быстрого запуска Azure Load Balancer для шаблонов ARM распространенных развертываний.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Чем правила NAT для входящего трафика отличаются от правил балансировки нагрузки?
Правила NAT используются для указания серверного ресурса для маршрутизации трафика. Например, можно настроить конкретный порт подсистемы балансировки нагрузки для отправки трафика RDP на определенную виртуальную машину. Правила балансировки нагрузки используются для указания пула ресурсов серверной части для маршрутизации трафика, балансировки нагрузки в каждом экземпляре. Например, правило подсистемы балансировки нагрузки может маршрутизировать пакеты TCP через порт 80 балансировщика нагрузки в пуле веб-серверов.

## <a name="next-steps"></a>Next Steps
Если ваш вопрос не указан выше, отправьте отзыв об этой странице с вашим вопросом. Это приведет к ошибке GitHub для группы разработки продукта, чтобы обеспечить ответ на все наши ценные вопросы клиентов.

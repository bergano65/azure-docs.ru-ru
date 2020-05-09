---
title: Часто задаваемые вопросы — Azure Load Balancer
description: Ответы на часто задаваемые вопросы о Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884492"
---
# <a name="frequently-asked-questions"></a>Вопросы и ответы

## <a name="what-types-of-load-balancer-exist"></a>Какие типы Load Balancer существуют?
Внутренние подсистемы балансировки нагрузки, которые балансируют трафик в виртуальной сети и внешних подсистемах балансировки нагрузки, которые сбалансировать трафик между конечной точкой, подключенной к Интернету, и из нее Дополнительные сведения см. в разделе [типы Load Balancer]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types)(. 

Для обоих этих типов Azure предлагает базовый SKU и SKU уровня "Стандартный", которые имеют различные возможности для работы, производительности, безопасности и отслеживания работоспособности. Эти различия объясняются в [сравнении номеров SKU]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) (статья.

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

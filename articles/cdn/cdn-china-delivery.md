---
title: Доставка содержимого для пользователей в Китае с помощью Azure CDN | Документация Майкрософт
description: Сведения о доставке содержимого пользователям в Китае с помощью сети доставки содержимого Azure (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: ce136a341809b1a485e86337f428b4685eba22f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681514"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Доставка содержимого для пользователей в Китае с помощью Azure CDN

Глобальная сеть доставки содержимого Azure (CDN) может предоставлять содержимое пользователям из Китая с помощью точек подключения (POP), расположенных вблизи Китая, или любой POP, которая обеспечивает наилучшую производительность для запросов, поступающих из Китая. Однако, если Китай является значительным рынком для ваших клиентов и им нужна быстрая производительность, вместо этого используйте сеть Azure CDN для Китая.

Сеть Azure CDN для Китая отличается от глобальной сети Azure CDN тем, что она доставляет содержимое из точек POP внутри Китая, сотрудничая с локальными поставщиками. По причине требования соответствия и нормативов в Китае вы должны зарегистрировать отдельную подписку для использования сети Azure CDN для Китая, а ваши веб-сайты должны иметь лицензию ICP. Выполнение доставки содержимого, а также управление ею с помощью портала и API идентичны доставке и управлению с помощью глобальной сети Azure CDN и сети Azure CDN для Китая.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Сравнение глобальной сети Azure CDN и сети Azure CDN для Китая

Для глобальной сети Azure CDN и сети Azure CDN для Китая доступны следующие возможности:

- Глобальная сеть Azure CDN:

     - Портал: https://portal.azure.com  

     - Выполняет доставку содержимого за пределы Китая

     - Четыре ценовые категории: Стандартное решение корпорации Майкрософт, стандарт Verizon, Verizon уровня "премиум" и стандарт Akamai

     - [Документация](https://docs.microsoft.com/azure/cdn/)

- Сеть Azure CDN для Китая

     - Портал: https://portal.azure.cn

     - Выполняет доставку содержимого внутри Китая

     - Две ценовые категории: "Стандартный" и "премиум"

     - [Документация](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о сети доставки содержимого Azure для Китая см. по следующим ссылкам:

- [Сеть доставки содержимого (CDN)](https://www.azure.cn/en-us/home/features/cdn/)

- [Общие сведения о сети доставки содержимого Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Использование сети доставки содержимого Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Сведения о доступности служб Azure в Китае](https://docs.microsoft.com/azure/china/china-get-started-service-availability)




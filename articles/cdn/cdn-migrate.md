---
title: Перенос профиля Azure CDN из Verizon категории "Стандартный" в Verizon категории "Премиум" | Документы Майкрософт
description: Сведения о переносе профиля из Verizon категории "Стандартный" в Verizon категории "Премиум".
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
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 7768dde424aedc295b53512db50c9dfc9db9ab8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635641"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Перенос профиля Azure CDN из Verizon категории "Стандартный" в Verizon категории "Премиум"

При создании профиля сети доставки содержимого (CDN) Azure для управления конечными точками Azure CDN предоставляет четыре доступных продукта. Сведения о различных продуктах и их функциях см. в разделе [Сравнение функций продуктов Azure CDN](cdn-features.md).

Если вы создали профиль **Azure CDN категории "Стандартный" из Verizon** и используете его для управления конечными точками, вы можете обновить этот профиль до **Azure CDN категории "Премиум" из Verizon**. При обновлении конечные точки CDN и все данные будут сохранены. 

> [!IMPORTANT]
> После обновления до профиля **Azure CDN категории "Премиум" из Verizon** вы не сможете преобразовать его в профиль **Azure CDN категории "Стандартный" из Verizon**.
> 

Чтобы обновить профиль до **Azure CDN категории "Стандартный" из Verizon**, обратитесь в [службу поддержки корпорации Майкрософт](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Сравнение профилей
Профили **Azure CDN категории "Премиум" из Verizon** имеют следующие основные отличия от профилей **Azure CDN категории "Стандартный" из Verizon**:
- Для некоторых функций Azure CDN, таких как [сжатие](cdn-improve-performance.md), [правила кэширования](cdn-caching-rules.md) и [геофильтрация](cdn-restrict-access-by-country.md) вы не можете использовать интерфейс Azure CDN и должны использовать портал Verizon, нажав кнопку **Управление**.
- API: В отличие от с помощью стандартных Verizon, нельзя использовать API для управления этими функциями, доступных на портале Verizon уровня "премиум". Однако вы можете использовать API для управления другими распространенными функциями, такими как создание или удаление конечной точки, очистка или загрузка кэшированных ресурсов и включение или отключение пользовательского домена.
- Цены Verizon уровня "премиум" имеет другую структуру ценообразования для передачи данных, чем стандартные Verizon. Дополнительные сведения см. на странице [Цены на сеть доставки содержимого](https://azure.microsoft.com/pricing/details/cdn/).

Профили **Azure CDN категории "Премиум" из Verizon** включают следующие дополнительные возможности:
- [Проверки подлинности маркера](cdn-token-auth.md): Пользователи могут получить и использовать маркер для выборки защищенным ресурсам.
- [Обработчик правил](cdn-rules-engine.md): Позволяет настраивать способ обработки HTTP-запросов.
- Средства расширенной аналитики:
   - [Подробный анализ запросов HTTP](cdn-advanced-http-reports.md)
   - [Анализ производительности пограничного узла](cdn-edge-performance.md)
   - [Анализ в реальном времени](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об обработчике правил см. в [Справке по обработчику правил Azure CDN](cdn-rules-engine-reference.md).

